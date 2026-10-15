# Combat Shrine: Attack Rating Compounds

From D2MOO (the open-source Diablo II 1.10 server rebuild). **Not tested in game yet**, and D2R may have fixed it.

## 1. Short version
While one Combat Shrine is active, taking **another** Combat Shrine recomputes the AR bonus from the AR that already includes the first bonus:
- 1 shrine: total AR = **3×**
- 2 shrines: **7×**
- 3 shrines: **15×** (2^(n+1) − 1)

The +200% damage is a fixed value and does **not** compound.

## 2. Why (two pieces of code disagree about the same number)
1. On click, the bonus is computed first (`D2Game/src/OBJECTS/ObjMode.cpp:2177`): `sub_6FC77750(..., 19, dwArg0, pPlayer)` reads your **current total AR** (`D2Common/src/Units/Units.cpp:2411` `UNITS_GetAttackRate` → `STAT_TOHIT`). The old shrine's state is still attached, so **the old bonus is included**.
2. When creating the state, the game finds the same state already present and **reuses it** (`D2Game/src/SKILLS/Skills.cpp:1320-1331`), then **overwrites** the AR bonus with the new number (`ObjMode.cpp:2190` `STATLIST_SetStat(..., STAT_TOHIT, nValue)`).
3. Result: new bonus = 2 × (base AR + old bonus), roughly doubling with each shrine.

The bonus is also a fixed snapshot taken **at click time**. Anything boosting AR at that moment (gear, Fanaticism, a merc aura) gets multiplied in, and the bonus stays for the shrine's duration (2400 frames = 96 s) even after you remove it.

## 3. How
1. Take a Combat Shrine.
2. Within **96 s**, take a **different** Combat Shrine (the same shrine has a 5-minute reset).
3. Keep going if you find more.
4. Stack AR first: wear AR gear and turn on auras before clicking, then swap back.

## 4. When it helps
- Hit chance caps at **95%**, so it only helps **low-AR melee against high-defence bosses**.
- E.g. a melee character fresh into Nightmare or Hell, or a Paladin/Barbarian/Druid short on AR gear.
- Useless for casters.

## 5. Reproducibility: 4/5
- Needs two Combat Shrines within reach in 96 s; shrine types are random.

## 6. How to test
- Take one Combat Shrine and note the character sheet AR; within 96 s take another, and check whether AR is about 7× the original (not 3×).
- Control: one shrine only should give 3×.

## 7. Source
| What | Where |
|---|---|
| Combat Shrine bonus computed from current AR | `D2Game/src/OBJECTS/ObjMode.cpp:2177`, `D2Common/src/Units/Units.cpp:2411` |
| Same state already present → reused | `D2Game/src/SKILLS/Skills.cpp:1320-1331` |
| AR bonus overwritten | `ObjMode.cpp:2190` |
| Shrine parameters | D2R `shrines.txt` (Combat Shrine arg0 = 200, duration 2400 frames) |
