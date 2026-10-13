# Act 3 Jungle Maps: Spider Forest, Great Marsh, Flayer Jungle

Each jungle map is a **2 × 6 grid of tiles**. Rows go **1 (south, near Kurast Docks) to 6 (north)**. In each row, **a** is the top-left tile and **b** the bottom-right one.

**Exits to another jungle can only be at 6a, 5a, 5b, 3a, 3b.** This is fixed by the code (`DRLG_GenerateJunglesAttachPoints`), not random. **6b is never a jungle exit.** The entrance from the previous jungle is always on **row 1** (1a or 1b).

| Exit tile | Where the next jungle is |
|---|---|
| 6a | directly north |
| 5a / 5b | west / east, shifted 2/3 north |
| 3a / 3b | west / east, shifted 1/3 north |

---

## 1. Spider Forest

```
  Spider Forest: chance of a jungle exit at each tile

                                                                 /\
                                                               /    \
                                                             /        \
                                                           /\  6a 31%  /\
                                                         /    \      /    \       (north)
                                                       /        \  /        \
                                                     /\  5a 28%  /\ 6b never /
                                                   /    \      /    \      /
                                                 /        \  /        \  /
                                               /\    4a    /\  5b 28%  /
                                             /    \      /    \      /
                                           /        \  /        \  /
                                         /\  3a 28%  /\    4b    /
                                       /    \      /    \      /
                                     /        \  /        \  /
                                   /\    2a    /\  3b 28%  /
                                 /    \      /    \      /
                               /        \  /        \  /
                              \    1a    /\    2b    /
                                \      /    \      /
  from Kurast Docks =>            \  /        \  /
                                    \    1b    /
                                      \      /
                                        \  /
```

- Each percentage is the chance **that tile has a jungle exit**. Spider Forest has 1 or 2 jungle exits.
- **1 exit (58.9%):** it leads to the Great Marsh, and you have to go through it.
- **2 exits (41.1%):** **you can skip the Great Marsh.** The **more northern** exit leads to Flayer Jungle (**6a > 5 > 3**). If both exits are on the same row (3a + 3b, or 5a + 5b), you can't tell which one it is.

---

## 2. Great Marsh

```
  Great Marsh: entrance (row 1) and exit to Flayer Jungle

                                                                 /\
                                                               /    \
                                                             /        \
                                                           /\  6a 13%  /\
                                                         /    \      /    \       dead end (no exit)
                                                       /        \  /        \
                                                     /\  5a 13%  /\ 6b never /
                                                   /    \      /    \      /
                                                 /        \  /        \  /
                                               /\    4a    /\  5b 13%  /
                                             /    \      /    \      /
                                           /        \  /        \  /
                                         /\  3a 10%  /\    4b    /
                                       /    \      /    \      /
                                     /        \  /        \  /
                                   /\    2a    /\  3b 10%  /
                                 /    \      /    \      /
                               /        \  /        \  /
                              \  1a 56%  /\    2b    /
                                \      /    \      /
entrance (1a or 1b) =>            \  /        \  /
                                    \  1b 44%  /
                                      \      /
                                        \  /
```

- **Entrance:** 1a 56%, 1b 44%. 1b means the Great Marsh is attached **west** of the previous jungle; 1a means north or east.
- **Dead end 41%:** if Spider Forest had 2 exits, the Great Marsh has **no** exit to Flayer Jungle. Turn back.
- **Otherwise it has exactly one exit** to Flayer Jungle, at 6a / 5a / 5b / 3a / 3b (percentages shown), never 6b.

---

## 3. Flayer Jungle

```
  Flayer Jungle: entrance (row 1)

                                                                 /\
                                                               /    \
                                                             /        \
                                                           /\  Kurast  /\
                                                         /    \      /    \       => Lower Kurast
                                                       /        \  /        \
                                                     /\    5a    /\  Kurast  /
                                                   /    \      /    \      /
                                                 /        \  /        \  /
                                               /\    4a    /\    5b    /
                                             /    \      /    \      /
                                           /        \  /        \  /
                                         /\    3a    /\    4b    /
                                       /    \      /    \      /
                                     /        \  /        \  /
                                   /\    2a    /\    3b    /
                                 /    \      /    \      /
                               /        \  /        \  /
                              \  1a 66%  /\    2b    /
                                \      /    \      /
entrance (1a or 1b) =>            \  /        \  /
                                    \  1b 34%  /
                                      \      /
                                        \  /
```

- **Entrance:** 1a 66%, 1b 34%.
- Flayer Jungle has **no exit to another jungle**. Its top row (6a / 6b) is the **exit to Lower Kurast**.

---

## How these were calculated
Every possible jungle layout was enumerated as the code builds it (`DRLG_GenerateJungles`, `sub_6FD83970`, `DRLG_GenerateJunglesAttachPoints` in D2MOO's `DrlgOutPlace.cpp`). The 3 chunks are 64×192 each, and all 5 attach directions point north. Where two chunks end up level, which one becomes the Great Marsh isn't fixed, so that case was counted 50/50.

Source: D2MOO, the open-source rebuild of the Diablo II 1.10 code. See also `flayer-skip.md`.
