# Act 1 Maps: Exit Chances

All directions are **on screen** (the isometric view): top-left, top-right, bottom-left, bottom-right.
Everything here comes from simulating D2MOO's map code (the open-source Diablo II 1.10 rebuild) with the real random number generator, 600,000 games. **Not tested in game yet.**

---

## 1. Cold Plains: where are Stony Field and Burial Grounds?

### The rule (from walking in from Blood Moor)

| Exit | Right | Straight | Left |
|---|---|---|---|
| **Stony Field** | **50%** | 25% | 25% |
| **Burial Grounds** | 9% | 34% | **56%** |

So: **turn right for Stony Field**, and **Burial Grounds is usually on the left**.

### Why
1. The game places **Cold Plains next to Stony Field first**, on a random side (25% each).
2. Then it places **Blood Moor next to Cold Plains**, on a random side. If that side is Stony Field's, it **doesn't re-roll; it moves to the next side clockwise**.
3. So the side clockwise from Stony Field gets Blood Moor twice as often (50%). Standing at the Blood Moor entrance, that puts Stony Field **on your right** 50% of the time.
4. Burial Grounds is placed last. It **can never be on Blood Moor's side** (a hard rule in the code) and can't overlap Stony Field, which pushes it to the left.

### Maps: Blood Moor on each side

Each map is Cold Plains. Each side shows the chance Stony Field / Burial Grounds is there.

#### Blood Moor at the bottom-left
```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                          Stony Field 25%   /                    \  Stony Field 25%
                     Burial Grounds 56%   /                        \  Burial Grounds 34%
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <               Cold Plains              >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                             = Blood Moor   \                    /  Stony Field 50%
                                              \                /  Burial Grounds 9%
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

#### Blood Moor at the top-left
```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                             = Blood Moor   /                    \  Stony Field 25%
                                          /                        \  Burial Grounds 56%
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <               Cold Plains              >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                          Stony Field 50%   \                    /  Stony Field 25%
                          Burial Grounds 9%   \                /  Burial Grounds 35%
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

#### Blood Moor at the top-right
```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                          Stony Field 50%   /                    \  = Blood Moor
                      Burial Grounds 9%   /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <               Cold Plains              >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                          Stony Field 25%   \                    /  Stony Field 25%
                         Burial Grounds 34%   \                /  Burial Grounds 56%
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

#### Blood Moor at the bottom-right
```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                          Stony Field 25%   /                    \  Stony Field 50%
                     Burial Grounds 34%   /                        \  Burial Grounds 9%
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <               Cold Plains              >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                          Stony Field 25%   \                    /  = Blood Moor
                         Burial Grounds 56%   \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

### Does the town exit help?

Only a little. "Your turn in Blood Moor" = the direction you walk out of the Rogue Encampment, compared to the direction you walk into Cold Plains.

| Your turn in Blood Moor | Games | Stony Field right / straight / left | Burial Grounds right / straight / left |
|---|---|---|---|
| Turned right | 45% | 55% / 22% / 22% | 10% / 33% / 57% |
| Went straight | 41% | 46% / 27% / 27% | 9% / 36% / 56% |
| Turned left | 14% | 44% / 28% / 28% | 8% / 36% / 56% |

If you turned right in Blood Moor, Stony Field is on the right 55% of the time. Otherwise it's about 45%. Burial Grounds doesn't change.

### Bonus: Cold Plains predicts Act 4
All acts use **the same game seed**, so Act 1's random numbers come back in Act 4. The sides of Stony Field and Blood Moor around Cold Plains decide **Plains of Despair's direction 100% in 75% of games**. See [act4-maps.md](act4-maps.md).

---

## 2. Barracks → Jail Level 1

- The Jail entrance is **straight ahead or to the left, 50/50**. The roll happens **after** the maze is built, using the Barracks' own seed.
- **The Forge (Hephasto) is always on the other side** from the Jail entrance.
- **Earlier maps can't predict it.** Each indoor level has its own seed, and the number of draws before the exit roll depends on how the maze grew.

---

## 3. Catacombs

| Level | Entrance | Exit |
|---|---|---|
| Level 1 | any of the 4 directions | 25% each |
| Level 2 | east–west or north–south corridor (the level's 2nd draw) | 25% each; **the waypoint sits one step past the exit, so leaving the waypoint room, the exit is on your right** |
| Level 3 | east–west or north–south | 25% each; the entrance corridor is **opposite to Level 2's about 58%** of the time |

**Earlier maps can't predict it.** Like the Barracks, each level has its own seed, and the exit roll comes after a variable number of maze draws.

---

## 4. Source code (D2MOO)

| What | Where |
|---|---|
| Act 1 link table and order (Stony Field → Cold Plains → Blood Moor → Rogue Encampment → Burial Grounds) | `D2Common/src/Drlg/DrlgOutPlace.cpp` → `gAct1WildernessDrlgLink` |
| Cold Plains / Burial Grounds placement (roll `& 3`, then next side clockwise) | `sub_6FD81380` |
| Blood Moor placement (side `& 3`, alignment `& 1`) | `sub_6FD81950` |
| Rogue Encampment placement and its allowed table | `sub_6FD81720`, `sub_6FD82050` |
| No overlap, Burial Grounds ≠ Blood Moor side, backtracking | `sub_6FD82050`, `sub_6FD823C0` |
| Same seed for every act | `DrlgOutPlace.cpp:1756` (link seed copied from the DRLG seed) |

## 5. Caveats
- **Source version:** D2MOO is the 1.10 code, not D2R's own program. D2R is widely believed to keep the old map generator (seeds give the same maps), but that isn't proven here.
- Sizes are from D2R `Levels.txt`. **Not tested in game.**
