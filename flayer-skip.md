# Flayer Skip (Great Marsh Skip)

> **Short version**
> - In **Act 2**, where **Dry Hills** attaches to **Rocky Waste** predicts whether Act 3 lets you skip the Great Marsh (73% vs 9%).
> - In **Act 3**, the moment you see **Spider Forest's exits** you know for sure: **2 jungle exits = you can skip**, and the more northern one leads to Flayer Jungle.

---

## 1. Predicting it in Act 2: Rocky Waste → Dry Hills

```
                                                             /\
                                                           /    \
                                                         /        \
              d2 = 0 (73%) / TR Town exit: random (52%)/            \d4 = 0 (73%)
                                                     /                \
                                                   /                    \
                                                 /                        \
                                  d3 = 1 (9%)  /                            \  d5 = 1 (9%)
                                             /                                \
                                           /                                    \
                                         <               Rocky Waste              >
                                           \                                    /
                                             \                                /
    d0 = 0 (73%) / TL Town exit: random (52%)  \                            /  d6 = 0 (73%)
                                                 \                        /
                                                   \                    /
                                                     \                /
                                            d1 = 1 (9%)\            /d7 = 1 (9%)
                                                         \        /
                                                           \    /
                                                             \/
```

| Label | Chance you can skip the Great Marsh |
|---|---|
| **0** | **73%** |
| **1** | **9%** |
| **random** | **52%** (no information) |

**TR / TL Town exit** = which way **Lut Gholein** exits to Rocky Waste:

| Lut Gholein exit | Dry Hills never at (town in the way) | Random slot |
|---|---|---|
| **TR** (top-right) | d0, d1 | **d2** |
| **TL** (top-left) | d6, d7 | **d0** |

**Why:** every act's map uses the same seed. Act 2's Dry Hills direction (`R & 7`) and Act 3's "where does jungle chunk 2 attach" (`R & 1`) are the **same random number** (the 5th draw), so they share the same parity. If Dry Hills' slot overlaps the town, the game moves it to the next slot instead of re-rolling. That's why the slot after the town is "random".

---

## 2. Confirming it in Act 3: Spider Forest's exits

Each jungle map is **2 × 6 tiles**. Rows are numbered **1 (south, near the docks) to 6 (north)**; **a** = the top-left tile of a row, **b** = the bottom-right one.

**Exits to another jungle can only be at these 5 tiles** (fixed by the code, not random):

| Exit tile | Where the next jungle is |
|---|---|
| **6a** | directly north |
| **5a** / **5b** | west / east, shifted 2/3 north |
| **3a** / **3b** | west / east, shifted 1/3 north |

**6b is never an exit**, in Spider Forest or the Great Marsh.

### Rule
| Spider Forest has | Chance | Result |
|---|---|---|
| **1 jungle exit** | 58.9% | Must go through the Great Marsh |
| **2 jungle exits** | 41.1% | **Can skip.** Take the **more northern** exit: **6a > 5 > 3** |

| Two exits | Chance | Exit to Flayer Jungle |
|---|---|---|
| 3a + 5b | 5.4% | **5b** |
| 3b + 5a | 5.4% | **5a** |
| 3a + 6a / 3b + 6a | 5.1% each | **6a** |
| 5a + 6a / 5b + 6a | 4.7% each | **6a** |
| 3a + 3b | 5.7% | can't tell (same height) |
| 5a + 5b | 5.0% | can't tell (same height) |

### The Great Marsh
- **Its entrance is always on row 1** (1a or 1b).
- **In the skip case it's a dead end:** no exit to Flayer Jungle. Turn back.
- **Otherwise it has exactly one exit to Flayer Jungle**, at 6a / 5a / 5b / 3a / 3b (never 6b).

---

## 3. Why Spider Forest decides it

`DRLG_GenerateJungles` builds the jungle from **3 chunks**:
- Chunk 0 is at the docks. Chunk 1 attaches to chunk 0. Chunk 2 attaches to chunk 0 or chunk 1.
- **All 5 attach directions go north.** So chunk 0 is always the southernmost.
- The chunks are sorted south → north into **Spider Forest → Great Marsh → Flayer Jungle**.
- If chunk 2 attaches to chunk 0, Spider Forest touches both others, so you can skip. If it attaches to chunk 1, the middle chunk is the Great Marsh, so you can't.

| | Chance you can skip |
|---|---|
| Act 3 roll even | 78.6% |
| Act 3 roll odd | 3.4% |
| Nothing known | 41.0% |

Act 2's numbers are slightly lower (73% / 9%). Act 2 re-rolls the Tal Rasha tombs 1 time in 7, which shifts the random sequence and breaks the link.

---

## 4. Caveats
- Source: **D2MOO**, the open-source rebuild of the Diablo II 1.10 code. The Act 2 predictor matches community maps, so D2R uses the same generator.
- Act 2 area collisions after Dry Hills aren't included in the 73% / 9% (they push it slightly toward 41%).

## 5. Code references (D2MOO)
| What | Location |
|---|---|
| Same seed for every act | `D2Game/src/GAME/Level.cpp:360` |
| Act 2 tombs / Act 3 Kurast rolls | `D2Common/src/Drlg/DrlgDrlg.cpp` → `DRLG_AllocDrlg` |
| Rocky Waste / Dry Hills placement | `D2Common/src/Drlg/DrlgOutPlace.cpp` → `sub_6FD81B30`, `sub_6FD81530`, `sub_6FD815E0` |
| Jungle chunks | `DrlgOutPlace.cpp` → `DRLG_GenerateJungles`, `sub_6FD83970` |
| Exit tiles (6a / 5a / 5b / 3a / 3b) | `DrlgOutPlace.cpp` → `DRLG_GenerateJunglesAttachPoints` (`switch (pJungleBranch->field_10)`) |
