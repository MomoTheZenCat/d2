# Baal Wave Skip: How It Really Works and the Exact Timing

> **Short version:** the skip happens on your **first re-entry** after Baal's laugh.
> Step back in **2.84–4.72 s after the laugh sound ends**, stay **0.12–0.44 s**, then get out.
> Wait **at least ~5.3 s after the laugh ends**, then go in again. If Baal laughs, the wave was skipped.

---

## 1. The four rules behind it

All times are game frames: 25 frames per second, 40 ms per frame.

### Rule 1: the laugh starts a 10-second countdown
When the throne room is clear, Baal does three things on the same frame:
- plays his laugh (`baallaugh1` = **7.36 s**, `baallaugh2` = **7.16 s**, picked at random),
- sets a "laughed" flag,
- starts a **250-frame (10.00 s)** countdown until he casts the summon.

So the countdown runs out **2.64 s (laugh 1) or 2.84 s (laugh 2) after the laugh sound ends**.

### Rule 2: with nobody nearby, Baal freezes but the countdown keeps going
"Nearby" means Baal's room is the room you're in or one next to it. The "line on the floor" players mention is just the edge where his room drops out of that set. It has nothing to do with Baal's line of sight.

- When you step out, the game deletes Baal's AI events and he stops thinking completely.
- The countdown is an absolute game-frame number, so it **keeps running while he's frozen**.
- When you step back in, he thinks again **2 frames (0.08 s) later**. If the countdown has run out, he **casts the summon on the spot**, and **the wave counter goes up as the cast starts**.

### Rule 3: the wave shows up about 3 seconds after the cast
- The cast animation (`41S3HTH`, 23 frames) releases the summon missile at **frame 14 (0.56 s)**.
- The missile flies for **60 frames (2.40 s)**.
- The wave spawns only when the missile lands, **about 3.0 s after the cast starts**.

### Rule 4: Baal's room unloads about 5 seconds after you leave
- Every **12 frames (0.48 s)** the game checks whether anyone is near Baal's room.
  - Someone nearby: the idle counter resets to 0.
  - Nobody nearby: the counter goes up by 1.
- After **11 empty checks in a row**, **4.84–5.28 s after you left**, the room unloads. When that happens:
  - Baal keeps **only his wave counter**. The "laughed" flag and the countdown are thrown away.
  - Any summon missile still in flight is **deleted without spawning anything**.
- ⚠️ You only count as present if you're there **on a check frame**. An entry shorter than 0.48 s can fall between two checks and go **completely unnoticed**.

---

## 2. Why the skip works

1. Your first re-entry wakes Baal **after** his countdown has run out, so he starts the cast and **the wave counter goes up right away**.
2. You leave **before the next check sees you**, so the unload countdown that started when you first left keeps running.
3. The room unloads **while the missile is still in the air**. The wave is deleted, but **the counter has already moved on**.

---

## 3. Timeline

**t = 0 is the moment the laugh sound ends and you step out past the boundary.**

| Time after laugh ends | What happens |
|---|---|
| **0 s** | Step out. Baal freezes; his countdown keeps running. |
| 0 – 2.84 s | Stay out. Entering now does nothing (countdown not done) and wastes the attempt. |
| **2.84 – 4.72 s** | **⭐ FIRST ENTRY: step in and straight back out.** Baal starts the summon cast 0.08 s after you enter. Wave counter +1. |
| 4.84 – 5.28 s | Room unloads. The summon missile in flight is deleted. **Stay out.** |
| **≥ 5.3 s, any time** | **SECOND ENTRY, just to check.** Baal laughs right away = **success**. The next wave will be the next boss. |

**Aim for about 3.5–4.0 s after the laugh ends.** 4.5 s is right at the late edge, which is why it works only some of the time.

If you step out **X seconds after** the laugh ends instead of right at the end, the first-entry window becomes:

```
[ 2.84 s , X + 4.72 s ]   (measured from the end of the laugh)
```

The early edge stays put and the late edge moves later.

---

## 4. How long the first entry must be

**Stay 3–11 frames (0.12–0.44 s). Shorter is better.**

| Duration | Result |
|---|---|
| < 0.12 s | You leave before he wakes up, so he never casts. Nothing happens. |
| 0.12 – 0.44 s | Works, as long as no 12-frame check lands while you're inside. |
| ≥ 0.48 s | A check always lands. The unload countdown resets, the missile lands and the wave spawns. |

The checks run on a fixed 12-frame cycle you can't see, so even perfect timing is partly luck:

| Time inside | Chance no check lands |
|---|---|
| 0.12 s (3 frames) | ~75 % |
| 0.16 s (4 frames) | ~67 % |
| 0.24 s (6 frames) | ~50 % |

Expect roughly **1 in 3 or 4 well-timed attempts to fail anyway**.

---

## 5. The second entry: anything late is fine, early ruins it

After a successful first entry, the result is **locked in once the room unloads**. Baal is stored with the higher wave counter and stays that way however long you wait.

| Second entry | Result |
|---|---|
| **< ~5.3 s after the laugh ends** | ❌ The room hasn't unloaded yet. Entering resets the unload countdown, the missile lands and the wave spawns. This undoes a skip that had worked. |
| **≥ ~5.3 s (no upper limit)** | ✅ Safe. Baal laughs = success. A wave is waiting = the first entry failed. |

Leave **at least 1.5–2 s after the first entry** before going back in.

---

## 6. Reading the result

| What you see | Meaning |
|---|---|
| Baal laughs on the **first** entry | You stayed out too long (≥ 4.84 s). The room had already unloaded and reset, and the wave counter didn't change. You get the **same wave** again. |
| Nothing on the first entry, **laugh on the second** | ✅ **Wave skipped.** |
| Nothing on the first entry, **wave on the second** | ❌ Either you entered too early (< 2.84 s, he didn't cast), or a check caught you (too long inside, or bad luck). |

If an attempt fails, the next round starts with Baal's next laugh. If he resets and laughs instead, you get the same wave again.

---

## 7. Practical tips

- **Walk or run instead of teleporting.** You need to be inside for only 3–11 frames, and walking across the boundary and back gives finer control.
- **Never open a Town Portal in or next to Baal's room.** A room with a portal never unloads, so the skip **can't work**.
- **Clear every monster near the throne first.** Baal waits for *all* living monsters within range to be gone, not just the wave, before he laughs.
- **Record a few attempts.** Use the end of the laugh as t = 0 and check what time your first entry actually landed.

---

## 8. Constants reference

| Constant | Value | Where it comes from |
|---|---|---|
| Server frame rate | 25 frames/s (40 ms) | Game engine |
| Laugh → summon countdown | 250 frames = 10.00 s | `BaalThrone` AI |
| Delay after a wave is cast | 100 frames = 4.00 s | `BaalThrone` AI |
| AI wake-up after you enter | 2 frames = 0.08 s | Monster AI scheduling |
| Cast animation `41S3HTH` | 23 frames, missile released at frame 14 | `AnimData.D2` |
| Summon missile (`baal spawn monsters`, id 550) flight | 60 frames = 2.40 s, spawns the wave when it ends (function 54) | `Missiles.txt` |
| Room presence check interval | 12 frames = 0.48 s | Game loop |
| Room unload | after 11 empty checks = 4.84–5.28 s | Room idle counter |
| Laugh sound length | 7.36 s (`baallaugh1`), 7.16 s (`baallaugh2`) | D2R voice files (English) |
| "Monsters still here" radius | 64 units, in nearby loaded rooms | `BaalThrone` AI |

### The five waves

| Wave | Boss | Monster type | Pack size |
|---|---|---|---|
| 1 | Colenzo the Annihilator | Fallen Shaman | 5 |
| 2 | Achmel the Cursed | Unraveler | 3 |
| 3 | Bartuc the Bloody | Council Member | 5 |
| 4 | Ventar the Unholy | Venom Lord | 8 |
| 5 | Lister the Tormentor | Minion of Destruction | 5 |

---

## 9. Source code references (D2MOO, Diablo II 1.10 server code)

| What | Location |
|---|---|
| Baal throne AI (laugh, countdown, cast, wave counter) | `D2Game/src/AI/AiThink.cpp` → `AITHINK_Fn134_BaalThrone` |
| "Monsters still nearby" count | `D2Game/src/AI/AiUtil.cpp` → `sub_6FCF16D0` |
| Wave spawned when the missile lands | `D2Game/src/MISSILES/MissMode.cpp` → `MISSMODE_SrvHit54_BaalSpawnMonsters` |
| AI frozen when you leave, re-armed when you enter | `D2Game/src/GAME/Level.cpp` → `LEVEL_AddClient` / `LEVEL_RemoveClient` |
| AI wake-up +2 frames | `D2Game/src/MONSTER/Monster.cpp` → `MONSTER_UpdateAiCallbackEvent` |
| Room presence check every 12 frames, unload after more than 10 empty checks | `D2Game/src/GAME/Game.cpp` → `GAME_UpdateProgress`; `D2Common/src/D2Dungeon.cpp` → `D2Common_10081_GetTileCountFromRoom` |
| Rooms with a portal never unload | `D2Common/src/Drlg/DrlgActivate.cpp` → `DRLGACTIVATE_TestRoomCanUnTile` |
| Only the wave counter saved on unload | `D2Game/src/UNIT/SUnitInactive.cpp` (`MONSTER_BAALTHRONE` branches) |
| Missiles deleted on unload, without spawning | `D2Game/src/UNIT/SUnitInactive.cpp`, `D2Game/src/UNIT/SUnit.cpp` → `SUNIT_RemoveUnit` |

---

## 10. Caveats

- **Source version:** the logic comes from **D2MOO**, the open-source rebuild of the **Diablo II 1.10** server code, not from D2R's own program. The D2R 3.3.94012 data matches it on every value this depends on: the `BaalThrone` AI, skill 286 "Baal Monster Spawn", missile 550 and its spawn function 54, the 60-frame missile flight, the 23-frame cast animation with release at frame 14, and super uniques 61–65 for the waves. That makes it **very likely** D2R behaves the same, but it isn't proven.
- **Laugh lengths** are measured from the **English** voice files and may include a little silence at the end. If the laugh you hear stops earlier than the file does, shift every time slightly later.
- **Real-world delay:** input lag, movement speed and online latency all add error on top of these server-frame times.
