# Souls Ritual: The Hidden Magic Find Easter Egg

> **Short version:** when **exactly 4** Will-o'-wisp-type monsters (Gloam, Swamp Ghost, Burning Soul, Black Soul) are near you and targeting you, any of them can randomly start a **ritual**.
> They spell out **7 shapes**. Every time **each wisp** finishes **each shape**, you get **+50 × (difficulty + 1)% magic find** for **19.2 hours of game time**.
> A full ritual in Hell gives **4200% MF**. It lasts until you leave the game.

---

## 1. Which monsters

All eight Will-o'-wisp variants use the same AI (`AI = WillOWisp` in D2R's `monstats.txt`), and they all count toward the ritual:

| Monster ID | Name | Skill |
|---|---|---|
| `willowisp1`, `willowisp5` | Gloam | Chain Lightning |
| `willowisp2` | Swamp Ghost | Chain Lightning |
| `willowisp3`, `willowisp7`, `willowisp8` | Burning Soul | Chain Lightning |
| `willowisp4`, `willowisp6` | Black Soul | Chain Lightning |

**Different variants can mix.** The game counts the base type (`willowisp1`), not the exact monster.

---

## 2. How it starts

Every time a wisp thinks, it checks all of the following (`AITHINK_Fn025_Willowisp_CheckConditionsForRitualOrBaptism`):

1. It **has a target**, and the target is **within 32 units**.
2. It's **not already** doing a ritual.
3. Its **cooldown has passed**: 1800 frames (**72 s**), set after it finishes a ritual or baptism.
4. A **random roll** succeeds:

| Difficulty | Chance per wisp, per think |
|---|---|
| Normal | 0.3 % |
| Nightmare | 0.4 % |
| Hell | 0.5 % |

When the roll succeeds, the wisp counts **every living wisp (itself included) within 32 units**, in its room and the adjacent rooms:

| Wisps counted | Result |
|---|---|
| **Exactly 4** | ✅ **Ritual**: 7 letter shapes, **gives MF** |
| **Exactly 5** | Baptism: a pentagram, **gives no MF** |
| Any other number | Nothing happens |

> **2 or 3 wisps can't start it, and 5 only do the pentagram.** The Reddit claims that "3 is enough" probably came from a ritual that had already started and then lost a wisp partway through (see section 4).

---

## 3. The 7 shapes

The shapes come in this order: **D → E → M → horns → R → P → S**.

The code comments guess these are the initials of Blizzard North people: **D**avid Brevik, **E**rich Schaefer, **M**ax Schaefer, **R**ick Seis, **P**hil Shenk, **S**tieg Hedlund. The horns are a demon symbol.

For each shape:
- Each wisp walks to its assigned spot, which is **offset from its target's position**. It has to get within 3 units of it.
- On a shared beat of **67 frames (2.68 s)**, it casts **3 times** at a point near you.
- Then it moves to the next shape. All four wisps wait for a shared **337-frame (13.5 s)** sync between shapes.

**A full ritual takes roughly 1.5–2 minutes.** That's an estimate from those timers.

---

## 4. How much MF

The buff is granted **every time each wisp finishes each shape**. Each grant is worth:

```
+50 × (difficulty + 1) % magic find
```

| Difficulty | Per wisp, per shape | Full ritual (4 wisps × 7 shapes = 28 grants) |
|---|---|---|
| Normal | +50 % | **1400 %** |
| Nightmare | +100 % | **2800 %** |
| Hell | +150 % | **4200 %** |

- Each wisp grants **on its own**. If one dies partway, the other three keep going and you just get less.
- **Duration:** `24 × 60 × 60 × 20` frames. The code comment calls this 20 hours, but at 25 frames/s it's **19.2 hours of game time**.
- Each grant is a timed stat list with its own expiry event. It **isn't saved**, so it's **gone when you leave the game**.
- Magic find has strong **diminishing returns** for set, rare and unique items, but not for magic (blue) items.

---

## 5. Why mercenaries and thorns break it

**This part is confirmed in the code:**
- Each wisp's ritual positions are calculated **from its current target's position**.
- The MF is only granted if, at the moment a shape finishes, **the target is a living player** (`pTarget->dwUnitType == UNIT_PLAYER && !SUNIT_IsDead(pTarget)`).

So:

| Situation | Effect |
|---|---|
| **The wisp targets your mercenary or a summon** | Its positions move to them and **the buff goes to nobody**. That's why people kill their mercenary first. |
| **Thorns, "when struck" effects, Thundergod's Vigor's Fist of the Heavens proc** | Likely make the wisp switch targets or get hit and lose its timing. *(Inferred, not traced in the code.)* |
| **You die** | The buff is only granted if you're alive when a shape finishes. Whether you lose buffs you already have on death is *unverified*. |
| **You leave long enough for the room to unload (about 5 s)** | The wisps' AI state is lost and the ritual is gone. Same rule as in the Baal wave-skip guide. |

---

## 6. How to do it

1. **Find wisps.** Worldstone Keep Level 2 has Burning Souls and Black Souls, and its waypoint makes checking quick.
2. **Get exactly 4 of them near you.** Kill the extras until 4 remain; with 5 you only get the pentagram.
   - Wisps are **immune to lightning**, so a lightning build can clear everything else without killing them.
3. **No mercenary, no summons, no thorns or "when struck" gear.** Every wisp must be targeting **you**.
4. **Stay within 32 units and let them attack you.** Starting the ritual is a random roll, so you just have to wait. Lightning absorb helps a lot.
5. **Let the ritual finish** (about 1.5–2 minutes) and **stay alive**.
6. Farm for up to 19 hours of game time **without leaving the game**.

---

## 7. Constants reference

| Constant | Value |
|---|---|
| Wisps needed for the ritual | **Exactly 4** within 32 units |
| Wisps that trigger the baptism instead | Exactly 5 (no MF) |
| Start chance per wisp per think | (difficulty + 3) / 1000 → 0.3 % / 0.4 % / 0.5 % |
| Cooldown after a ritual | 1800 frames = 72 s |
| Shapes | 7 (D, E, M, horns, R, P, S) |
| Casts per shape | 3 |
| Beat between casts | 67 frames = 2.68 s |
| Sync between shapes | 337 frames = 13.5 s |
| MF per wisp per shape | 50 × (difficulty + 1) % |
| Buff duration | 1,728,000 frames = 19.2 hours of game time |
| Server frame rate | 25 frames/s |

### Source code (D2MOO, Diablo II 1.10 server code)

| What | Location |
|---|---|
| AI states and constants | `D2Game/src/AI/AiThink.cpp` → `D2C_WillowispAIState`, `D2C_WillowispAIConstants` |
| Start conditions and roll | `AITHINK_Fn025_Willowisp_CheckConditionsForRitualOrBaptism` |
| Counting wisps (exactly 4 or 5) | `AITHINK_Fn025_Willowisp_State_AttemptRitual`, `AITHINK_UnitFindCallback_Willowisp` |
| Ritual shapes and the MF grant | `AITHINK_Fn025_Willowisp_State_Ritual` |
| Baptism (pentagram, no MF) | `AITHINK_Fn025_Willowisp_State_Baptism` |
| Dispatcher | `AITHINK_Fn025_Willowisp` |

---

## 8. Caveats

- **Source version:** the logic comes from **D2MOO**, the open-source rebuild of the **Diablo II 1.10** server code, not from D2R's own program. D2R 3.3.94012 still assigns the `WillOWisp` AI to all eight wisp variants, and the community numbers (150% per soul per letter in Hell, 4200% total, about 19 hours) match the code exactly. So D2R almost certainly uses the same logic, but it isn't proven.
- **The 1.5–2 minute ritual time and the effect of thorns and procs** are estimates or inferences, not measurements.
