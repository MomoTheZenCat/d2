# Act 2 Desert Maps: Exit Chances for Every Area

Each map is the **area you are in**. Slots d0–d7 show **the chance the next area is there**; `X` = impossible (blocked by an earlier area). The previous area is written **in the slot where it actually is** (`dX = <area>`).

**How to use:** check which slot (d0–d7) the current area was in relative to the previous one, then open the matching map.

Slot positions: top-right edge = d4 (upper), d5 (right); bottom-right edge = d6 (right), d7 (lower); bottom-left edge = d0 (left), d1 (lower); top-left edge = d2 (upper), d3 (left).

The high-chance slots (25–50%) are always **the slot right after a blocked one**: if the roll lands on a blocked slot, the game moves it to the next slot.

---

## 1. Rocky Waste → Dry Hills

### Lut Gholein exit top-left (TL)

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 13%/            \d4 13%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 13%  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <               Rocky Waste              >
                                        \                                    /
                                          \                                /
                                    d0 37%  \                            /  d6 = Lut Gholein
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 13%\            /d7 = Lut Gholein
                                                      \        /
                                                        \    /
                                                          \/
```

### Lut Gholein exit top-right (TR)

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 38%/            \d4 12%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 12%  /                            \  d5 13%
                                          /                                \
                                        /                                    \
                                      <               Rocky Waste              >
                                        \                                    /
                                          \                                /
                          d0 = Lut Gholein  \                            /  d6 12%
                                              \                        /
                                                \                    /
                                                  \                /
                                    d1 = Lut Gholein\            /d7 13%
                                                      \        /
                                                        \    /
                                                          \/
```

## 2. Dry Hills → Far Oasis

### Dry Hills is at Rocky Waste's d0

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 12%/            \d4 13%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 12%  /                            \  d5 = Rocky Waste
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                                    d0 12%  \                            /  d6 X
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 13%\            /d7 38%
                                                      \        /
                                                        \    /
                                                          \/
```

### Dry Hills is at Rocky Waste's d1

```
                                                          /\
                                                        /    \
                                                      /        \
                                                d2 X/            \d4 = Rocky Waste
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 25%  /                            \  d5 X
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                                    d0 12%  \                            /  d6 X
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 13%\            /d7 50%
                                                      \        /
                                                        \    /
                                                          \/
```

### Dry Hills is at Rocky Waste's d2

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 25%/            \d4 12%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 12%  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                                    d0 25%  \                            /  d6 13%
                                              \                        /
                                                \                    /
                                                  \                /
                                                d1 X\            /d7 = Rocky Waste
                                                      \        /
                                                        \    /
                                                          \/
```

### Dry Hills is at Rocky Waste's d3

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 19%/            \d4 12%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 12%  /                            \  d5 X
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                                    d0 31%  \                            /  d6 = Rocky Waste
                                              \                        /
                                                \                    /
                                                  \                /
                                               d1 6%\            /d7 19%
                                                      \        /
                                                        \    /
                                                          \/
```

### Dry Hills is at Rocky Waste's d4

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 25%/            \d4 13%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 12%  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                                    d0 25%  \                            /  d6 12%
                                              \                        /
                                                \                    /
                                                  \                /
                                    d1 = Rocky Waste\            /d7 X
                                                      \        /
                                                        \    /
                                                          \/
```

### Dry Hills is at Rocky Waste's d5

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 31%/            \d4 25%
                                                  /                \
                                                /                    \
                                              /                        \
                                      d3 X  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                          d0 = Rocky Waste  \                            /  d6 13%
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 13%\            /d7 6%
                                                      \        /
                                                        \    /
                                                          \/
```

### Dry Hills is at Rocky Waste's d6

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 12%/            \d4 25%
                                                  /                \
                                                /                    \
                                              /                        \
                          d3 = Rocky Waste  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                                      d0 X  \                            /  d6 13%
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 25%\            /d7 13%
                                                      \        /
                                                        \    /
                                                          \/
```

### Dry Hills is at Rocky Waste's d7

```
                                                          /\
                                                        /    \
                                                      /        \
                                    d2 = Rocky Waste/            \d4 X
                                                  /                \
                                                /                    \
                                              /                        \
                                      d3 X  /                            \  d5 50%
                                          /                                \
                                        /                                    \
                                      <                Dry Hills               >
                                        \                                    /
                                          \                                /
                                      d0 X  \                            /  d6 12%
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 25%\            /d7 12%
                                                      \        /
                                                        \    /
                                                          \/
```

## 3. Far Oasis → Lost City

### Far Oasis is at Dry Hills's d0

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 14%/            \d4 13%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 13%  /                            \  d5 = Dry Hills
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                                    d0 30%  \                            /  d6 X
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 11%\            /d7 19%
                                                      \        /
                                                        \    /
                                                          \/
```

### Far Oasis is at Dry Hills's d1

```
                                                          /\
                                                        /    \
                                                      /        \
                                                d2 X/            \d4 = Dry Hills
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 16%  /                            \  d5 32%
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                                    d0 10%  \                            /  d6 14%
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 15%\            /d7 13%
                                                      \        /
                                                        \    /
                                                          \/
```

### Far Oasis is at Dry Hills's d2

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 26%/            \d4 13%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 12%  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                                    d0 25%  \                            /  d6 11%
                                              \                        /
                                                \                    /
                                                  \                /
                                                d1 X\            /d7 = Dry Hills
                                                      \        /
                                                        \    /
                                                          \/
```

### Far Oasis is at Dry Hills's d3

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 12%/            \d4 13%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 13%  /                            \  d5 X
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                                    d0 18%  \                            /  d6 = Dry Hills
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 12%\            /d7 32%
                                                      \        /
                                                        \    /
                                                          \/
```

### Far Oasis is at Dry Hills's d4

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 28%/            \d4 13%
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 12%  /                            \  d5 13%
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                                    d0 24%  \                            /  d6 10%
                                              \                        /
                                                \                    /
                                                  \                /
                                      d1 = Dry Hills\            /d7 X
                                                      \        /
                                                        \    /
                                                          \/
```

### Far Oasis is at Dry Hills's d5

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 20%/            \d4 25%
                                                  /                \
                                                /                    \
                                              /                        \
                                      d3 X  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                            d0 = Dry Hills  \                            /  d6 12%
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 18%\            /d7 12%
                                                      \        /
                                                        \    /
                                                          \/
```

### Far Oasis is at Dry Hills's d6

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 26%/            \d4 25%
                                                  /                \
                                                /                    \
                                              /                        \
                            d3 = Dry Hills  /                            \  d5 12%
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                                      d0 X  \                            /  d6 13%
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 13%\            /d7 11%
                                                      \        /
                                                        \    /
                                                          \/
```

### Far Oasis is at Dry Hills's d7

```
                                                          /\
                                                        /    \
                                                      /        \
                                      d2 = Dry Hills/            \d4 X
                                                  /                \
                                                /                    \
                                              /                        \
                                    d3 24%  /                            \  d5 11%
                                          /                                \
                                        /                                    \
                                      <                Far Oasis               >
                                        \                                    /
                                          \                                /
                                    d0 13%  \                            /  d6 22%
                                              \                        /
                                                \                    /
                                                  \                /
                                              d1 13%\            /d7 18%
                                                      \        /
                                                        \    /
                                                          \/
```

## 4. Lost City → Valley of Snakes

Valley of Snakes is placed with no offset, so it can only be at **d0, d2, d4, d6** (aligned to Lost City's west or north edge). **d1, d3, d5, d7 never happen.** For running, **d2 and d4 are both at the top corner** (one on each side of it), so each map also gives their total.

### Lost City is at Far Oasis' d0

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 25%/            \d4 25%
                                                  /                \
                                                /                    \
                                              /                        \
                                  d3 never  /                            \  d5 = Far Oasis
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                                    d0 30%  \                            /  d6 21%
                                              \                        /
                                                \                    /
                                                  \                /
                                            d1 never\            /d7 never
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 50%**

### Lost City is at Far Oasis' d1

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 20%/            \d4 = Far Oasis
                                                  /                \
                                                /                    \
                                              /                        \
                                  d3 never  /                            \  d5 never
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                                    d0 28%  \                            /  d6 52%
                                              \                        /
                                                \                    /
                                                  \                /
                                            d1 never\            /d7 never
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 20%**

### Lost City is at Far Oasis' d2

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 29%/            \d4 25%
                                                  /                \
                                                /                    \
                                              /                        \
                                  d3 never  /                            \  d5 never
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                                    d0 21%  \                            /  d6 25%
                                              \                        /
                                                \                    /
                                                  \                /
                                            d1 never\            /d7 = Far Oasis
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 54%**

### Lost City is at Far Oasis' d3

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 24%/            \d4 18%
                                                  /                \
                                                /                    \
                                              /                        \
                                  d3 never  /                            \  d5 never
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                                    d0 58%  \                            /  d6 = Far Oasis
                                              \                        /
                                                \                    /
                                                  \                /
                                            d1 never\            /d7 never
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 42%**

### Lost City is at Far Oasis' d4

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 25%/            \d4 25%
                                                  /                \
                                                /                    \
                                              /                        \
                                  d3 never  /                            \  d5 never
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                                    d0 30%  \                            /  d6 20%
                                              \                        /
                                                \                    /
                                                  \                /
                                      d1 = Far Oasis\            /d7 never
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 50%**

### Lost City is at Far Oasis' d5

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 37%/            \d4 34%
                                                  /                \
                                                /                    \
                                              /                        \
                                  d3 never  /                            \  d5 never
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                            d0 = Far Oasis  \                            /  d6 29%
                                              \                        /
                                                \                    /
                                                  \                /
                                            d1 never\            /d7 never
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 71%**

### Lost City is at Far Oasis' d6

```
                                                          /\
                                                        /    \
                                                      /        \
                                              d2 30%/            \d4 18%
                                                  /                \
                                                /                    \
                                              /                        \
                            d3 = Far Oasis  /                            \  d5 never
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                                    d0 20%  \                            /  d6 32%
                                              \                        /
                                                \                    /
                                                  \                /
                                            d1 never\            /d7 never
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 48%**

### Lost City is at Far Oasis' d7

```
                                                          /\
                                                        /    \
                                                      /        \
                                      d2 = Far Oasis/            \d4 38%
                                                  /                \
                                                /                    \
                                              /                        \
                                  d3 never  /                            \  d5 never
                                          /                                \
                                        /                                    \
                                      <                Lost City               >
                                        \                                    /
                                          \                                /
                                    d0 31%  \                            /  d6 31%
                                              \                        /
                                                \                    /
                                                  \                /
                                            d1 never\            /d7 never
                                                      \        /
                                                        \    /
                                                          \/
```
**For running: top corner (d2 + d4) = 38%**

---

**Source:** a 600,000-game simulation of Act 2's link code as written in D2MOO (the open-source 1.10 rebuild): `DrlgOutPlace.cpp` → `sub_6FD81B30`, `sub_6FD81530`, `sub_6FD81BF0`, `sub_6FD815E0`, plus the overlap check and backtracking in `DRLGOUTPLACE_LinkAct2Outdoors`. Sizes from D2R `Levels.txt`: Lut Gholein 56×56; Rocky Waste, Dry Hills, Far Oasis, Lost City 80×80; Valley of Snakes 32×32; same on all difficulties.
