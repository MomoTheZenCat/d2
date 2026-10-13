# Conversion Bugs: 1/256 HP and Permanent Conversion

Two separate bugs in **Paladin Conversion** (and, for the second, **Assassin Mind Blast**), both explained by the code:

1. **1/256 HP:** when a converted monster **outlevels the Paladin**, it comes back from conversion with **1/256 of its max HP**.
2. **Permanent conversion:** **convert, then leave the game quickly and rejoin**, and the monster stays on your side **forever**. This works for **both Paladin and Assassin**, but **only in multiplayer** (the game has to keep running while you're gone).

---

## 1. How conversion works

**Conversion** (`SKILLS_SrvDo079_Conversion`, `D2Game/src/SKILLS/SkillPal.cpp:1110-1222`) and **Mind Blast** (`D2Game/src/SKILLS/SkillAss.cpp:2223-2290`) both do the same thing to the target:

1. Set its alignment to **GOOD** (`sub_6FCBDD30(pTarget, 2, 1)`).
2. Link it into the caster's target group with `D2GAME_UpdateSummonAI_6FC401F0(..., caster->dwNodeIndex)`. The monster's **`dwNodeIndex` becomes the caster's slot (0–7)**. It is **not** added to the caster's pet list.
3. Attach a **timed stat list** (state `conversion`, owner = the caster) with a **remove callback**, and schedule an `EVENTTYPE_REMOVESTATE` event for when it expires.
4. **Only if the monster's level is higher than the caster's:**
   - save the monster's original **level** and **max HP** (as a real value, already `>> 8`) in a `STATE_CONVERSION_SAVE` stat list;
   - scale its current and max HP **down** by `caster level / monster level`;
   - set its level to the caster's level.

| Skill | Duration (D2R `skills.txt`) |
|---|---|
| Conversion (Paladin, srvdofunc 79) | `Param1` 400 frames = **16 s** |
| Mind Blast (Assassin, srvdofunc 51) | `Param3` 150 frames + random up to 100 = **6–10 s** |

When the timer runs out, the remove callback sets the monster back to **EVIL** and, if step 4 happened, restores its level and HP.

---

## 2. Bug 1: 1/256 max HP (Paladin only)

**When:** the converted monster's **level is higher than the Paladin's**, so the original HP was saved (step 4).

**Cause:** HP stats are stored in **fixed-point** (multiplied by 256). On expiry, `SKILLS_StatRemoveCallback_Conversion` (`SkillPal.cpp:1231-1255`):
- writes **current HP** back correctly, with `<< 8`;
- writes **max HP** back as the saved real value, **without `<< 8`**:

```c
STATLIST_SetUnitStat(pUnit, STAT_HITPOINTS, nNewHp << 8, 0);      // correct
STATLIST_SetUnitStat(pUnit, STAT_MAXHP, nConversionMaxHp, 0);     // missing << 8
```

So the monster's max HP becomes **1/256** of the original, and its current HP is clamped down to that.

**Assassin Mind Blast doesn't have this bug.** Its restore (`SKILLS_StatRemoveCallback_MindBlast`, `SkillAss.cpp:2206`) correctly uses `nConversionMaxHp << 8`.

### How to use it
1. Use a Paladin whose **level is lower than the monster's**. This happens naturally in Nightmare and Hell, or on a low-level Paladin.
2. **Convert** the monster.
3. Let the conversion **expire normally** (16 s), with you **nearby** so the room stays loaded.
4. The monster turns hostile again with **about 0.4 % of its max HP**. One hit kills it.

---

## 3. Bug 2: permanent conversion (Paladin and Assassin, multiplayer only)

### Step by step
1. **You convert the monster.** It becomes GOOD and joins your target group (node 0–7). It is **not** one of your pets.
2. **You leave the game.** `CLIENTS_RemoveClientFromGame` (`D2Game/src/GAME/Clients.cpp:667-678`) frees **your pets**, but the converted monster isn't one. Then `D2GAME_FreeUnitNode_6FC40310` (`D2Game/src/GAME/Targets.cpp:107-129`) moves **every unit in your target group to node 11**.
   - The converted monster is now **GOOD, ownerless, at node 11**, and still carries its conversion timer.
3. **Its room unloads** (about 5 s with no player nearby). `SUNITINACTIVE_CompressUnitIfNeeded` (`D2Game/src/UNIT/SUnitInactive.cpp`):
   - forces a monster back to EVIL **only if `dwNodeIndex < 8`** (`:614-626`). At node 11 that's **skipped**;
   - runs callbacks only for **curse-type** states (`:776`). Conversion apparently isn't one;
   - **saves its alignment as GOOD** (`:829-840`) and its current max HP and HP (`:855`);
   - **throws away** the conversion timer and the event **without running the remove callback**.
4. **The room loads again later.** The monster is spawned fresh at its normal level and **forced to GOOD** (`:230`). It has **no conversion timer** and belongs to no target group.

### End state
- **Permanently on the players' side.** It never reverts, and it can't be converted again (both skills need an EVIL target). It lasts until it dies or the game ends.
- It keeps its own AI and **fights evil monsters**. Players can't attack it.
- **Level:** back to normal (fresh spawn). **Max HP:** the saved value. If it outlevelled you, that's the **scaled-down** HP, *not* 1/256, because the callback never ran.
- **Kill credit:** it has no owner, so its kills probably credit nobody. *(Plausible, not traced fully.)*

### Recipe
1. Convert the monster.
2. **Leave the game before the conversion runs out**, with **no other player near that monster**:
   - Conversion: within about **11 s** (16 s duration − about 5 s for the room to unload).
   - Mind Blast: within about **1–4 s** (6–10 s duration − about 5 s).
3. **The game must keep running** while you're gone, so another player has to stay in it (somewhere else).
4. **Rejoin** and go back to the monster.

If another player stays near the monster, its room never unloads, the timer expires normally and the monster reverts. With Conversion, that's when Bug 1 hits.

### Single-player
**Not possible.** In single-player, leaving destroys the game, and no other offline path moves a converted monster to node 11.

### Offline side effect: the monster you leave behind gets weaker
A converted monster **isn't a pet**, so it **doesn't follow you** through a Town Portal or waypoint.
- If you convert a monster that **outlevels you** and leave its area (Town Portal, waypoint, or teleport away) **within about 10 s**, its room unloads while it's still converted.
- At node 0–7 it's **set back to EVIL** and saved with its **scaled-down max HP**, but it comes back at its **original level**.
- Repeating the cycle (convert, leave, return) **shrinks its HP again** each time.

---

## 4. Reference

| Constant | Value |
|---|---|
| Conversion duration | 400 frames = 16 s |
| Mind Blast conversion duration | 150 + random 0–99 frames = 6–10 s |
| Room unload with no player nearby | about 4.84–5.28 s |
| Server frame rate | 25 frames/s |
| HP fixed-point scale | × 256 (`<< 8`) |

### Source code (D2MOO, Diablo II 1.10 server code)

| What | Location |
|---|---|
| Conversion (Paladin) | `D2Game/src/SKILLS/SkillPal.cpp` → `SKILLS_SrvDo079_Conversion` |
| Conversion expiry, missing `<< 8` | `SkillPal.cpp` → `SKILLS_StatRemoveCallback_Conversion` |
| Mind Blast (Assassin), correct `<< 8` | `D2Game/src/SKILLS/SkillAss.cpp` → `SKILLS_StatRemoveCallback_MindBlast` |
| Converted monster joins the caster's target group | `D2Game/src/GAME/Targets.cpp` → `D2GAME_UpdateSummonAI_6FC401F0` |
| Leaving player moves its group to node 11 | `Targets.cpp` → `D2GAME_FreeUnitNode_6FC40310`; `D2Game/src/GAME/Clients.cpp` → `CLIENTS_RemoveClientFromGame` |
| Room unload: node < 8 reset, alignment saved, timers dropped | `D2Game/src/UNIT/SUnitInactive.cpp` → `SUNITINACTIVE_CompressUnitIfNeeded` |
| Restore as GOOD with no timer | `SUnitInactive.cpp` → `SUNITINACTIVE_RestoreInactiveUnits` |

---

## 5. Caveats

- **Source version:** this comes from **D2MOO**, the open-source rebuild of the **Diablo II 1.10** server code, not from D2R's own program. D2R 3.3.94012 still uses the same server functions (Conversion 79, Mind Blast 51), and both tricks are reported by the community. But D2R adds a new Conversion parameter ("Use sumskill1 on convert expire?", set to 0), so its conversion code has changed somewhat and the details might differ.
- **Permanent conversion assumes the `conversion` state isn't curse-flagged.** If it were, the room-unload code would run its callback first and the trick wouldn't work. That `states.txt` flag wasn't checked; the trick's reported success suggests it isn't.
