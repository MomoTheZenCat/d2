# Reanimated Horde: Self-Resurrects Give Full XP and Drops Again

> **Short version:** when a Reanimated Horde-type monster dies, it can **stand back up by itself** up to **2 times**. Unlike every other monster resurrection in the game, the code **deliberately clears the "no XP" and "no drop" flags** right after the resurrect.
> So each self-resurrected Horde gives **full experience and a new drop roll** again, up to **3 kills per monster**. **Champions** and **boss minions** do it too; **bosses** don't.

---

## 1. Which monsters

All six variants share the base type `reanimatedhorde1` and the `ReanimatedHorde` AI (D2R `monstats.txt`):

| ID | Name | Monster level (N / NM / H) | Revive chance (N / NM / H) | Revive delay (N / NM / H) |
|---|---|---|---|---|
| `reanimatedhorde1` | Rot Walker | 31 / 61 / 81 | 30 / 22 / 15 % | 12.0 / 18.0 / 22.0 s |
| `reanimatedhorde2` | Reanimated Horde | 33 / 62 / 82 | 30 / 22 / 15 % | 16.0 / 22.0 / 26.0 s |
| `reanimatedhorde3` | Prowling Dead | 35 / 63 / 83 | 30 / 22 / 15 % | 20.0 / 26.0 / 28.0 s |
| `reanimatedhorde4` | Unholy Corpse | 40 / 64 / 84 | 30 / 22 / 15 % | 24.0 / 28.0 / 32.0 s |
| `reanimatedhorde5` | Defiled Warrior | 42 / 65 / 85 | 30 / 22 / 15 % | 28.0 / 32.0 / 34.0 s |
| `reanimatedhorde6` | Unholy Corpse | 42 / 65 / 85 | **0 % (no `aip8`)** | — |

- **Revive chance** is `aip8`: rolled **once each time the monster dies**.
- **Revive delay** is `10 × aip1 + 1` frames after death, at 25 frames/s.
- ⚠️ **`reanimatedhorde6` never revives.** Its `aip8` is empty, so the chance is 0.

---

## 2. Where they spawn (and whether they revive)

From D2R's `Levels.txt`. "Normal" means the Normal monster list; "NM/Hell" means the Nightmare/Hell list.

| Area | Normal | Nightmare / Hell | Revives in Hell? |
|---|---|---|---|
| **Frozen River** | Rot Walker | Rot Walker | ✅ 15 % |
| **Glacial Trail** | Reanimated Horde | Rot Walker | ✅ 15 % |
| **The Ancients' Way** | — | Reanimated Horde | ✅ 15 % |
| **Icy Cellar** | Prowling Dead | — | — |
| **Halls of Anguish** (Nihlathak's Temple) | Prowling Dead (+ boss packs of Reanimated Horde) | Prowling Dead | ✅ 15 % |
| **Halls of Pain** | Unholy Corpse (+ boss packs of Prowling Dead) | — | — |
| **Halls of Vaught** | — | Prowling Dead | ✅ 15 % |
| **Infernal Pit** (Anya's red portal) | — | `reanimatedhorde6` | ❌ **never** |
| **Worldstone Keep Level 1** | Defiled Warrior | `reanimatedhorde6` | ❌ **never** |

> **Best spots for Hell:** **Halls of Anguish**, **Halls of Vaught**, **The Ancients' Way**, **Frozen River**, **Glacial Trail**.
> **Not Worldstone Keep Level 1 or the Infernal Pit:** on Nightmare and Hell they use the variant that never revives.

---

## 3. How it works

### Step 1: the death roll
When the monster enters its death mode, the "after death" monster-mod handler `sub_6FC6DD20` (`D2Game/src/MONSTER/MonsterUnique.cpp:1675-1695`):
- only runs for **evil** monsters that are **dying**;
- deletes any pending monster-mod events;
- rolls `rand % 100 < aip8`. On success it schedules the resurrect for **`10 × aip1 + 1` frames later**.

### Step 2: the resurrect
When the event fires, `sub_6FC6DDE0` (`MonsterUnique.cpp:1697-1735`) checks, in order:
1. the monster is **still dead**, and isn't in an "undead death" state;
2. it is **not a boss** (`nTypeFlag & MONTYPEFLAG_UNIQUE`, `0x08`). **Champions (`0x04`) and boss minions (`0x10`) are allowed**;
3. it isn't on the player's side;
4. its corpse spot isn't blocked (monster path mask: walls, objects, doors, summons). **If blocked, it waits another `10 × aip1 + 1` frames and retries**;
5. it has resurrected **fewer than 2 times** (AI param 1), and the **corpse still exists** (not consumed by Corpse Explosion, Raise Skeleton and so on).

Then it casts **Self Resurrect** and immediately does:

```c
pUnit->dwFlags &= 0xFBFDFFFFu;   // clears UNITFLAG_NOXP (0x04000000) and UNITFLAG_NOTC (0x00020000)
```

### Why the flags matter
- The resurrect skill's start function runs **at the moment of the cast** (mode change → `sub_6FD12FD0`, `MonsterMode.cpp:1277/1582`). It calls `SKILLS_ResurrectUnit`, which **sets `NOXP | NOTC`** on the monster (`D2Game/src/SKILLS/SkillMonst.cpp:1267`). That's the normal "resurrected monsters give nothing" rule.
- The Horde handler **clears both flags right after**, so the resurrected Horde gives **full XP** and **rolls its drop table** again.
- **Every other resurrection keeps the flags** (Fallen Shamans, Greater Mummies, Radament, Druid/Necro revives), so they give nothing the second time.

The flag clearing is written on purpose, so this is **probably intended design**, not a bug. It just isn't widely documented.

---

## 4. How much it's worth

Each death rolls separately, with at most 2 resurrects. Expected kills per monster:

| Difficulty | Chance | Expected kills per monster | Extra XP and drops |
|---|---|---|---|
| Normal | 30 % | 1 + 0.30 + 0.09 = **1.39** | **+39 %** |
| Nightmare | 22 % | 1 + 0.22 + 0.048 = **1.27** | **+27 %** |
| Hell | 15 % | 1 + 0.15 + 0.023 = **1.17** | **+17 %** |

**Champions are the best targets:** champion XP and their better drop table, repeated on each resurrect.

---

## 5. How to use it

1. Farm one of the areas in section 2 that **revive on your difficulty**.
2. **Don't destroy the corpses.** No Corpse Explosion, no corpse-consuming skills, and no shattering (cold kills can shatter).
3. **Keep your summons and mercenary off the corpses.** A blocked spot only delays the resurrect.
4. **Wait for the delay:** **12–34 seconds** depending on the variant and difficulty (section 1). Clear nearby, then come back to kill whatever stood up.
5. Prioritise **champion packs**.

---

## 6. Things to check in-game

- **Whether champions and boss minions actually get the self-resurrect mod.** In D2MOO, the code that gives the Horde this mod is a commented-out 1.14 stub (`D2Game/src/MONSTER/MonsterSpawn.cpp:1030`). It checks only the **base type** (`MONSTER_REANIMATEDHORDE1`), which suggests every quality gets it. The handler only excludes bosses.
- **That the second kill really gives XP and a drop.** Watch the experience bar on the second kill.
- **The exact delay** (section 1).

---

## 7. Source code (D2MOO, Diablo II 1.10 server code)

| What | Location |
|---|---|
| Death roll and scheduling | `D2Game/src/MONSTER/MonsterUnique.cpp` → `sub_6FC6DD20` |
| Resurrect conditions and the flag clear | `MonsterUnique.cpp` → `sub_6FC6DDE0` |
| `SKILLS_ResurrectUnit` sets `NOXP \| NOTC` | `D2Game/src/SKILLS/SkillMonst.cpp:1267` |
| Skill start runs during the mode change | `D2Game/src/MONSTER/MonsterMode.cpp:1277/1582` → `sub_6FD12FD0` |
| Monster type flags | `D2Common/include/D2Monsters.h:32-35` |
| Self-resurrect mod assignment (commented stub) | `D2Game/src/MONSTER/MonsterSpawn.cpp:1030` |

## 8. Caveats
- **Source version:** this comes from **D2MOO**, the open-source rebuild of the **Diablo II 1.10** server code, not D2R's own program. D2R 3.3.94012 data matches: same AI (`ReanimatedHorde`), same chance and delay parameters.
- **Not tested in-game.** It's well known that Horde revive. Whether the revived ones give XP and drops again hasn't been verified here.
