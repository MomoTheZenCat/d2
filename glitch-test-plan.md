# Glitch Test Plan: Two Collision Leaks

Two glitches predicted from reading the Diablo II 1.10 server code (D2MOO). **Neither has been confirmed in-game.** The goal is to find out whether they happen in D2R 3.3.94012, and how noticeable they are.

Both come from the same kind of bug: a monster skill marks tiles in the room's **collision grid** when it starts, and the mark either isn't fully cleared afterwards or isn't cleared at all.

Useful background:
- The collision grid works in **subtiles**. A unit's footprint is usually a 5-subtile plus shape.
- Marks in the grid stay **until the room unloads**, which happens about **5 seconds after you've left the area** (more precisely, once no room near you is that room). Coming back rebuilds the grid without the marks.
- Server frame rate is 25 frames/s.

---

## Test 1: Necromancer Revive cap leaves an invisible obstacle

### Prediction
When a **revived "nest" monster** (Vile Mother, Stygian Hag, Grotesque) is killed by the **Revive cap** *while it's in the middle of summoning*, its summon spot stays marked:
- **one subtile of `NO_PATH`** at the centre, which **blocks walking** for players and monsters;
- a **5-subtile plus of the monster bit** (`COLLIDE_MONSTER`), which **stops your missiles that collide with monsters**. They burst on empty ground.

### Mechanism (code refs)
1. The Nest skill's start function (`SKILLS_SrvSt49_Nest_EvilHutSpawner`, `D2Game/src/SKILLS/SkillMonst.cpp:689-690`) marks the spawn spot (monster bit + `NO_PATH`) and sets the caster *uninterruptible*.
2. Only the end of the spawn (`SrvDo091`, `SkillMonst.cpp:707, 723`) clears that mark.
3. Reviving while at the cap kills your **oldest** revive through `SUNITDMG_KillMonster` (`D2Game/src/PLAYER/PlayerPets.cpp:~165, 223-225`). That kill **skips the uninterruptible check** and goes straight to death mode (`D2Game/src/UNIT/SUnitDmg.cpp:1801-1862`), so step 2 never runs.
4. Missiles whose collision type includes the monster bit (types 2, 3, 5 and 8) are traced against the grid, and a path that's cut short **bursts with no target** (`D2Game/src/MISSILES/MissMode.cpp:72-83, 664-667`; `PathMisc.cpp:377-405`).

### Affected monsters (D2R data)
`monstats2` marks these as revivable, and their Skill1 is **Nest**:
- `vilemother1-3`: Vile Mother, Stygian Hag, Grotesque
- `crownest1-4`: their nest variants

A revived pet uses Skill1 **80 % of the time** when it has a target (`D2Game/src/AI/AiThink.cpp:8835-8838`).

### Setup
- **Character:** Necromancer with **Revive**. The revive cap equals Revive's skill level.
- **Location:** an area with Vile Mothers / Stygian Hags / Grotesques and enemies nearby, so the revived mother has a target to summon at.
- **Recommended:** a **ranged** test character or a second skill whose missile collides with monsters (for example a normal arrow, bolt or Bone Spear), to check the missile-eating effect.

### Steps
1. Kill a Vile Mother (or Hag / Grotesque) and **revive her first**, so she's your **oldest** revive.
2. Revive other corpses until you're **at the cap**.
3. Let the mother fight. Watch for her **summon (Nest) animation**.
4. **During that animation, revive one more corpse.** That kills the oldest revive, the mother, mid-summon.
5. Note where she was summoning: the spawn point is next to her.

### What to check
| Check | Expected if the glitch is real |
|---|---|
| Walk your character onto the spot | **Blocked** by an invisible single-subtile obstacle; you walk around it |
| Lure a monster across the spot | It **paths around it** |
| Shoot monster-colliding missiles through the spot with no monster there | They **burst on the empty spot** |
| Teleport onto or over it | Probably **not blocked** (flight ignores `NO_PATH`) |
| Leave the area for more than about 5 s, then come back | The obstacle is **gone** |

### Pass / fail
- **Confirmed:** walking is blocked, *or* missiles burst, on an empty spot where the mother was summoning.
- **Not confirmed:** nothing happens after 10+ clean attempts.
- **Extra check:** whether a summon still appears when the mother dies mid-animation. The code predicts it **does** (the spawn has no "is the caster dead" check), with no XP or drops.

### Things that can spoil a run
- **Timing:** the Revive has to land *during* the summon animation. Recording video helps confirm it.
- **Which pet dies:** make sure the mother really is the **oldest** revive.
- It's **unverified** whether a revived mother actually summons successfully as a pet. If she never summons, the test can't happen.
- The spot is tiny (one subtile of `NO_PATH`), so walk slowly across the area to find it.

---

## Test 2: Monster leaps leave invisible spots that block monster missiles

### Prediction
Every **normal monster leap** leaves a **5-subtile plus of the player bit** (`COLLIDE_PLAYER`) on its landing spot. The spot **doesn't block walking**, but **monster missiles aimed at players** (collision types 1, 3 and 8) **burst on it**, as if you were standing there. It works as a temporary **missile shield** for you.

### Mechanism (code refs)
- At takeoff, the leap start functions (`SKILLS_SrvSt40_Leap` and `SKILLS_SrvSt41_LeapAttack`, `D2Game/src/SKILLS/SkillBar.cpp:1281, 1593`) set the **player bit** on the landing spot for **monsters too**.
- When a **monster** lands, the code clears the **monster bit** (`0x100`) instead (`SkillBar.cpp:1487`). The player bit stays.
- Monster missiles with collision types 1, 3 and 8 include the player bit in their path trace (`MissMode.cpp:72-83`), and a path cut short by it **bursts with no target** (`MissMode.cpp:664-667`).
- **Player Barbarians are not affected.** A player's landing clears the same bit it set (`SkillBar.cpp:1528`).

### Affected monsters (D2R data)
| Monster | Skill |
|---|---|
| `sandleaper1-7`: Sand Leaper, Cave Leaper, Tomb Creeper, Tree Lurker, Razor Pit Demon | MonLeap |
| **Korlic** (`ancientbarb3`) | MonLeapAttack |

### Best test scene: the Ancients fight ⭐
- **Korlic** leaps all the time, and **Madawc** throws **"ancient throwing axe"**, which has **collision type 3** (it checks the player bit).
- Every Korlic landing spot should **eat Madawc's axes** that fly through it.

### Steps (Ancients)
1. Start the Ancients fight with a sturdy character.
2. Watch where Korlic **lands**, and remember the spot.
3. Move so that the **spot is between you and Madawc**, ideally in a straight line, and **don't walk over the spot yourself** (see below).
4. Watch Madawc's axes as they cross the spot.

### What to check
| Check | Expected if the glitch is real |
|---|---|
| Madawc's axes crossing an old Korlic landing spot | **Burst at the spot** instead of reaching you |
| The same axes on a line with no landing spot | Reach you normally (control) |
| Walking through the spot | **Not blocked** (the player bit doesn't block walking) |
| After you walk over the spot | It's probably **erased**. Your own footprint uses the same bit and clears it when you step off *(inferred)* |
| Leave the area for more than about 5 s, then come back | **Gone** |

### Alternative scene
Sand Leapers or Tree Lurkers mixed with **ranged monsters** whose missiles collide with players. Note which missile types burst on the empty landing spots.

### Pass / fail
- **Confirmed:** monster missiles repeatedly burst on an **empty** spot where a leaper recently landed, while a control line nearby lets them through.
- **Not confirmed:** axes pass through landing spots normally across 10+ observations.

### Things that can spoil a run
- **Your own footprint** probably erases the spot if you walk over it, so keep off it.
- **Another unit standing on the spot** (merc, summon, monster) makes the missile hit that unit instead. Test with the spot empty.
- **Overlapping landings:** Korlic often lands near you, so pick a spot that's clearly away from you.
- Recording video at normal speed, with slow-motion review, makes the burst point clear.

---

## Caveats
- **Source:** both predictions come from **D2MOO**, the open-source rebuild of the **Diablo II 1.10** server code, not from D2R's own program. The D2R 3.3.94012 data matches on what matters here: the Nest skill and the revivable mothers, MonLeap / MonLeapAttack users, and Madawc's axe with collision type 3. D2R may have fixed either bug.
- **Test 1's trigger is unverified:** it depends on a revived mother actually summoning as a pet.
- **Test 2's spot erasure** (by your own footprint) is inferred, not traced.
