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

They also decide **Act 5**: Frigid Highlands and Frozen Tundra run along Stony Field's line (100%), and Arreat Plateau along Blood Moor's line. See [act5-caves.md](act5-caves.md).

---

## 2. Dark Wood → Black Marsh → Tamoe Highland ⭐

This part of Act 1 has its **own link list**, and it starts over from the **same random numbers** as Cold Plains. So Cold Plains tells you the way through Black Marsh.

### The rules
- **Tamoe Highland → Monastery Gate is always top-right.**
- **In Black Marsh, the exit to Tamoe Highland is on the same side as Stony Field is around Cold Plains (100%).**
  - Exception: if Stony Field is **bottom-left**, Tamoe Highland is **top-left** (top-right is blocked by the Monastery Gate, so it moves one step clockwise).
  - So in Black Marsh, Tamoe Highland is top-left 50%, top-right 25%, bottom-right 25%, never bottom-left.
- **Dark Wood joins Black Marsh on the same side as Blood Moor joins Cold Plains**, with two exceptions shown in the table.

### Full table (sides around Cold Plains → sides around Black Marsh)

| Stony Field | Blood Moor | Dark Wood joins Black Marsh at | Tamoe Highland exit | Walking in from Dark Wood |
|---|---|---|---|---|
| bottom-left | top-left | *50/50:* bottom-left → turn **left**, or top-right → turn **right** | **top-left** | left / right |
| bottom-left | top-right | top-right | **top-left** | **right** |
| bottom-left | bottom-right | bottom-right | **top-left** | **straight** |
| top-left | bottom-left | bottom-left | **top-left** | **left** |
| top-left | top-right | top-right | **top-left** | **right** |
| top-left | bottom-right | bottom-right | **top-left** | **straight** |
| top-right | bottom-left | bottom-left | **top-right** | **straight** |
| top-right | top-left | top-left | **top-right** | **left** |
| top-right | bottom-right | bottom-right | **top-right** | **right** |
| bottom-right | bottom-left | bottom-left | **bottom-right** | **right** |
| bottom-right | top-left | top-left | **bottom-right** | **straight** |
| bottom-right | top-right | bottom-left | **bottom-right** | **right** |

**11 of the 12 cases are 100% fixed** (about 87% of games). Without Act 1 info, walking into Black Marsh from Dark Wood, Tamoe Highland is to the **right 56%**, straight 25%, left 19%.

### The Forgotten Tower: check the top quarter first
Split Black Marsh into four quarters **on screen** (top, right, bottom, left corner):

```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \                ← check here first
                                            /       top 32%      \
                                          /    \              /    \
                                        /        \          /        \
                                      /            \      /            \
                                    /                \  /                \
                                  <     left 23%       X      right 22%    >
                                    \               /    \               /
                                      \           /        \           /
                                        \       /            \       /
                                          \   /                \   /
                                            \     bottom 23%     /
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

- **Top quarter 32%**, the other three about 23% each. The single most likely spot is the **cell just inside the top corner** (7–10%), then the cells next to it along the **top-right edge**.
- **Why:** the game "shuffles" the 8×8 interior cells with only 64 random swaps (not a real shuffle), so about 13% of cells never move. It then tries cells in that order, and the original order starts at the top corner and runs along the top-right edge. The waypoint and the 5 shrines are placed the same way just before the tower, so they sometimes take those cells first.
- It's **never within 8 tiles of the map edge**.
- Where it is inside a quarter can't be predicted from other maps.
- **Halves:** the **top-right half 55%** vs bottom-left half 45%; the **top-left half 56%** vs bottom-right half 44%.
- **Cold Plains + Stony Field shift this a little.** The tower uses Black Marsh's own level seed, not the Act 1 link numbers, but the Act 1 link directions decide whether Black Marsh gets a **river** along its bottom-right edge, and the river takes those cells first:

  | Around Cold Plains | Games | Top-left half | Quarters (top / left / right / bottom) |
  |---|---|---|---|
  | Stony Field **top-left** + Blood Moor **bottom-left** (always a river) | 6% | **62%** | 36% / 25% / 19% / 19% |
  | Stony Field **bottom-left** + Blood Moor **top-left** (river half the time) | 13% | **58%** | 34% / 24% / 21% / 21% |
  | Every other combination (no river) | 81% | 55% | 32% / 23% / 23% / 22% |

  The differences are small, as you'd expect from "lots of data, small but real difference".
- **Inside Black Marsh you can check it directly:** if you come in from Dark Wood on the **bottom-left** side and the Tamoe Highland exit is **top-left**, there's a river → top-left half **62%**. Any other entrance/exit pair: baseline 55%.
- **The town exit doesn't matter** (55–57% in every direction).

**Black Marsh waypoint (same bias, even stronger):** top quarter **35%**, the other three 22% each; the single cell just inside the top corner **13%**; with a river (the entrance bottom-left + Tamoe top-left case) top quarter **39%** and top-left half 63%. It's placed before the shrines and the tower, so nothing takes its cells first. Stony Field and Dark Wood waypoints follow the same 35% rule; Cold Plains is different (its waypoint is placed next to Blood Moor).

**Source:** `D2Common/src/Drlg/DrlgOutPlace.cpp` → `gAct1MonasteryDrlgLink` (Tamoe Highland always below the Monastery Gate via `sub_6FD81AD0`; Black Marsh and Dark Wood via `sub_6FD81380`; `sub_6FD82130` also keeps them clear of the Moo Moo Farm); `DrlgOutWild.cpp` (Black Marsh places `LVLPREST_ACT1_TOWER_1` first); `DrlgOutdoors.cpp` → `DRLGOUTDOORS_SpawnOutdoorLevelPreset`, `DRLGOUTDOORS_SpawnAct12Waypoint`, `DRLGOUTDOORS_SpawnAct12Shrines` (64-swap shuffle, first free cell). Directions checked with the real random number generator over 400,000 games; tower and waypoint quarters simulated over 200,000 games with 0–30% of cells blocked (the result doesn't change).

---

## 3. Barracks → Jail Level 1

- The Jail entrance is **straight ahead or to the left, 50/50**. The roll happens **after** the maze is built, using the Barracks' own seed.
- **The Forge (Hephasto) is always on the other side** from the Jail entrance.
- **Earlier maps can't predict it.** Each indoor level has its own seed, and the number of draws before the exit roll depends on how the maze grew.

---

## 4. Catacombs

| Level | Entrance | Exit |
|---|---|---|
| Level 1 | any of the 4 directions | 25% each |
| Level 2 | east–west or north–south corridor (the level's 2nd draw) | 25% each; **the waypoint sits one step past the exit, so leaving the waypoint room, the exit is on your right** |
| Level 3 | east–west or north–south | 25% each; the entrance corridor is **opposite to Level 2's about 58%** of the time |

**Earlier maps can't predict it.** Like the Barracks, each level has its own seed, and the exit roll comes after a variable number of maze draws.

---

## 5. Source code (D2MOO)

| What | Where |
|---|---|
| Act 1 link table and order (Stony Field → Cold Plains → Blood Moor → Rogue Encampment → Burial Grounds) | `D2Common/src/Drlg/DrlgOutPlace.cpp` → `gAct1WildernessDrlgLink` |
| Cold Plains / Burial Grounds placement (roll `& 3`, then next side clockwise) | `sub_6FD81380` |
| Blood Moor placement (side `& 3`, alignment `& 1`) | `sub_6FD81950` |
| Rogue Encampment placement and its allowed table | `sub_6FD81720`, `sub_6FD82050` |
| No overlap, Burial Grounds ≠ Blood Moor side, backtracking | `sub_6FD82050`, `sub_6FD823C0` |
| Same seed for every act | `DrlgOutPlace.cpp:1756` (link seed copied from the DRLG seed) |

## 6. Caveats
- **Source version:** D2MOO is the 1.10 code, not D2R's own program. D2R is widely believed to keep the old map generator (seeds give the same maps), but that isn't proven here.
- Sizes are from D2R `Levels.txt`. **Not tested in game.**
