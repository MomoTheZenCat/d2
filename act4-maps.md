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
So once you know **where Stony Field and Blood Moor are around Cold Plains**, Plains of Despair is **100% decided in 75% of games** and 50/50 in the rest.

| Stony Field (around Cold Plains) | Blood Moor (around Cold Plains) | Plains of Despair (around Outer Steppes) |
|---|---|---|
| bottom-left | top-left | top-right 50% / bottom-left 50% |
| bottom-left | top-right | **top-right 100%** |
| bottom-left | bottom-right | **bottom-right 100%** |
| top-left | bottom-left | **bottom-left 100%** |
| top-left | top-right | **top-right 100%** |
| top-left | bottom-right | **bottom-right 100%** |
| top-right | bottom-left | **bottom-left 100%** |
| top-right | top-left | **top-right 100%** |
| top-right | bottom-right | bottom-right 50% / top-right 50% |
| bottom-right | bottom-left | bottom-right 50% / bottom-left 50% |
| bottom-right | top-left | **top-right 100%** |
| bottom-right | top-right | **top-right 100%** |

**Short version:** Plains of Despair is on **the same side as Blood Moor**, except:
- If Blood Moor is **top-left**, Plains is **top-right** (top-left is the Fortress's side).
- If Blood Moor is **one step clockwise from Stony Field**, it's 50/50 between Blood Moor's side and Stony Field's side (top-left becomes top-right).

City of the Damned can't be predicted from Act 1 beyond its own rule in section 3.

## 5. River of Flame and Chaos Sanctuary

Fixed layouts. River of Flame is entered from the stairs in City of the Damned, and the Chaos Sanctuary layout is fixed. The seal variants are rolled separately and aren't covered here.

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
- **Not tested in game.** The Act 1 → Act 4 table is the easiest to check: note Stony Field and Blood Moor around Cold Plains, then look at Outer Steppes.
