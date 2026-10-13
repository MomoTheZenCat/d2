# Permanent Revives: Why a Frozen Revive Never Expires

> **Short version:** a Necromancer's Revive normally dies after **180 seconds**. The game kills it with a **one-time timer event**.
> If the revive is **frozen** at the exact moment that timer fires, the game **throws the event away and never reschedules it**, so the revive **stays forever** (until it dies, or you leave the game).
> The easy source of freezing is the Act 5 **Carver Shaman**: its ice spell is a Glacial Spike, which **freezes**.

---

## 1. How a revive normally expires

- When you cast **Revive** (`SKILLS_SrvDo058_Revive`, `D2Game/src/SKILLS/SkillNec.cpp:1490`), the game schedules a **one-time event** of type `EVENTTYPE_MONUMOD` for **revive time + duration** (`SkillNec.cpp:1621`).
- **Duration** in D2R's `skills.txt`: Revive `Param3` = **4500 frames = 180 s**. There's no per-level bonus.
- When the event fires, the game runs the "temporary summon" monster-mod handler (`sub_6FC6DFC0`, `D2Game/src/MONSTER/MonsterUnique.cpp:1750-1774`). It:
  - does nothing if the revive is already dead;
  - if the revive is **uninterruptible** (mid-skill), retries in 3 frames;
  - otherwise **kills the revive** (removes it from its Necromancer owner).
- Nothing else ever reschedules this event. **It only fires once.**

---

## 2. Why freezing stops it

The monster event dispatcher, `MONSTERMODE_EventHandler` (`D2Game/src/MONSTER/MonsterMode.cpp:2130-2150`), has this rule:

```c
switch (nEvent)
{
case EVENTTYPE_STATREGEN:
case EVENTTYPE_TRAP:
case EVENTTYPE_RESET:
case EVENTTYPE_PERIODICSKILLS:
case EVENTTYPE_REMOVESTATE:
    break;
default:
    if (STATES_CheckState(pUnit, STATE_FREEZE) && !SUNIT_IsDead(pUnit))
    {
        return;   // event dropped: not run, not rescheduled
    }
    break;
}
```

- While a monster is **frozen** (`STATE_FREEZE`) and alive, **every event except the five listed is dropped**.
- `EVENTTYPE_MONUMOD`, the revive's expiry, **isn't on the list**.
- So if the revive is **frozen on the exact frame its 180 s runs out**, the expiry event is dropped, and with no other expiry left, **the revive never dies of old age**.
- The revive still works normally afterwards. When the freeze ends, `SUNITDMG_RemoveFreezeState` (`D2Game/src/UNIT/SUnitDmg.cpp:102`) re-arms its AI.

---

## 3. Freeze vs. chill: it has to be a real freeze

The bug needs **`STATE_FREEZE`**. Most cold attacks only **chill** (`STATE_COLD`), and chill does **not** work.

In D2R's data, the monsters whose attacks actually **freeze** are:

| Monster | Freeze source | Where |
|---|---|---|
| **Carver Shaman** (`fallenshaman6`) ⭐ | Skill **ShamanIce**, which fires a **Glacial Spike** (`glacialspike`, freeze on hit) | **Arreat Plateau**, **The Ancients' Way** (Act 5, **Nightmare and Hell**) |
| Summoner | Glacial Spike | Arcane Sanctuary |
| Diablo | DiabCold | Chaos Sanctuary |
| Baal | Melee attack 2 (freeze element) | Worldstone Chamber |

The **Carver Shaman** is by far the most practical: it's a common monster in normal Act 5 areas, and it casts its ice spell freely.

### What can be frozen
`SUNITDMG_ApplyFreezeState` (`SUnitDmg.cpp:1645-1725`) only freezes a monster that:
- is **not uninterruptible** at that moment;
- is **not a boss or a mercenary** (those only get chilled);
- has a **negative `coldeffect`** for the current difficulty in `monstats.txt`. Most revivable monsters qualify.

Freeze duration on monsters is **divided by difficulty** (`MonsterFreezeDivisor` in `DifficultyLevels.txt`): **Normal ÷1, Nightmare ÷2, Hell ÷4**. So the window is shorter on harder difficulties.

---

## 4. How to do it

1. **Revive a monster** that can be frozen (negative `coldeffect`). Note the **time you revived it**.
2. Go to **Arreat Plateau** or **The Ancients' Way** on **Nightmare or Hell** and find **Carver Shamans**.
3. Around **2:55–3:00 after the revive**, make sure the revive is **fighting the shamans** and getting hit by their **Glacial Spikes**.
4. The revive needs to be **frozen at the 180-second mark** (4500 frames after the revive was cast).
5. If the revive is still alive a few seconds after 3:00, it worked, and it **won't expire**.

**Tips:**
- Revive several monsters at once. They all expire at about the same time, so one shaman's spikes can freeze several of them.
- A frozen revive takes extra damage and can shatter, so don't use your most valuable revive for practice.
- Revives aren't saved, so a permanent revive lasts **until it dies or you leave the game**.

---

## 5. Reference

| Constant | Value |
|---|---|
| Revive duration | 4500 frames = **180 s** (`skills.txt` Revive `Param3`) |
| Expiry event | `EVENTTYPE_MONUMOD`, fires **once** |
| Events kept while frozen | `STATREGEN`, `TRAP`, `RESET`, `PERIODICSKILLS`, `REMOVESTATE` |
| Freeze divisor on monsters | Normal 1 / Nightmare 2 / Hell 4 |
| ShamanIce freeze length (`skills.txt`) | `ELen` 125 frames, +50 per level, before the divisor |
| Server frame rate | 25 frames/s |

### Source code (D2MOO, Diablo II 1.10 server code)

| What | Location |
|---|---|
| Revive and its expiry event | `D2Game/src/SKILLS/SkillNec.cpp` → `SKILLS_SrvDo058_Revive` |
| Expiry handler (kills the revive) | `D2Game/src/MONSTER/MonsterUnique.cpp` → `sub_6FC6DFC0` |
| Frozen units drop events | `D2Game/src/MONSTER/MonsterMode.cpp` → `MONSTERMODE_EventHandler` |
| Who can be frozen, and the divisor | `D2Game/src/UNIT/SUnitDmg.cpp` → `SUNITDMG_ApplyFreezeState` |
| AI re-armed when the freeze ends | `D2Game/src/UNIT/SUnitDmg.cpp` → `SUNITDMG_RemoveFreezeState` |
| Glacial Spike freeze on hit | `D2Game/src/MISSILES/MissMode.cpp` → `MISSMODE_SrvHit13_GlacialSpike_HellMeteorDown` |

---

## 6. Caveats

- **Source version:** the logic comes from **D2MOO**, the open-source rebuild of the **Diablo II 1.10** server code, not from D2R's own program. D2R 3.3.94012's data matches on what matters here (Revive's 180 s duration, the Carver Shaman's ShamanIce Glacial Spike, the freeze divisors), and the trick is reported to work in D2R. But the exact behaviour isn't proven.
- **ShamanIce's exact freeze length at the shaman's skill level** wasn't calculated, so the window size per difficulty is approximate.
