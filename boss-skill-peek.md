# Baal and Diablo Peek at Your Selected Skills

From D2MOO (the open-source Diablo II 1.10 server rebuild). **Not tested in game yet**, and D2R may have changed it.

## 1. The check (shared by both)
`AI_CheckSpecialSkillsOnPrimeEvil` (`D2Game/src/AI/AiBaal.cpp:775`): on every think, it looks at **your currently selected right AND left skill**. Either one being on this list counts as "dangerous":
- Blizzard
- Meteor
- Fire Wall (skill level > 3)
- Immolation Arrow (skill level > 7)

**It checks what's selected at the moment he thinks**, not what you cast. Baal thinks about once a second; Diablo about the same.

## 2. Baal: teleports away and walks off more
Each think, Baal picks an action from a weight table (`AiBaal.cpp:840-905`).
Example: Hell, Baal at full life, you solo, within 25 tiles, no missile barrier between you, within 75 tiles of his spawn:

| Action | Normal | Dangerous skill selected |
|---|---|---|
| Idle | 25% | 22% |
| **Teleport 25 tiles away from you** | **5%** | **7.7%** |
| **Walk around within 12 tiles of you** | 1.3% | **7.7%** |
| Nova | 15% | 15% |
| Cold missiles | 15% | 13% |
| Inferno | 20% | 18% |
| Tentacles | 10% | 9% |

Dodging (teleport + walking off) goes from about 6% to about 15%.

**Other things that make Baal teleport more:**
- **A missile barrier between you, with a dangerous skill selected:** teleport goes from 0 to about 10%, plus cold missiles (`AiBaal.cpp:723-735`).
- **You more than 75 tiles from his spawn:** teleport weight +35. **Fight near his spawn.**
- **You more than 25 tiles from him:** teleport weight is fixed at 30, and no Nova.

## 3. Diablo: no teleport, but more charging and Fire Wall
Diablo has no teleport. With a dangerous skill selected **and you within 85 tiles of his spawn**, his weights change like this (`AiThink.cpp` `AITHINK_Fn051_Diablo`): run/charge 10 → 30, Fire Wall 0 → 15, Fire Nova +10.

Example: solo, within 25 tiles, fire resist = lightning resist, no missile barrier:

| Action | Normal | Dangerous skill selected |
|---|---|---|
| **Red lightning (laser)** | **29%** | **19%** |
| Fire Nova | 18% | 19% |
| Firestorm | 18% | 12% |
| **Charge at you** | **12%** | **23%** |
| **Fire Wall** | 0% | **12%** |
| Walk closer | 24% | 15% |

- With a dangerous skill selected, **less laser**, more charging in and fire.
- With a missile barrier between you (the "squat" branch), a dangerous skill only adds Fire Wall; squat drops from 6.7% to 5.6%.
- **More than 85 tiles from his spawn**, the selected skill makes no difference.

## 4. How to use it
- **Baal:** between casts, keep both slots on skills not on the list (Frozen Orb, Chain Lightning, Teleport), swapping with hotkeys. Saves about one or two chase-teleports per fight.
- **Diablo:** think the other way round. If **the laser is your problem**, keep Blizzard or Meteor selected and he trades some laser for charging. If you **want him to stay put by a pillar**, don't select them.
- Frozen Orb or lightning Sorceresses aren't affected anyway; Blizzard or Meteor builds feel it most.

## 5. How to test
- Stand near Baal for 2 minutes and count his teleports: once with Blizzard selected on the right, once with Frozen Orb, then compare.
- Diablo: same spot, count lasers and charges with Blizzard selected versus Frozen Orb.

## 6. Trivia
- Baal's curse depends on your life and mana: max life > max mana gets Defense curse, otherwise Blood Mana (`AiBaal.cpp` case 8).
- Diablo's "squat" (S4) only appears when a missile barrier is between you and him and you're not in melee range.

## 7. Item charges count too (any class can use it)
The check only looks at the selected skill's ID, so **charged skills from items count**. You don't have to cast them, just keep them selected (no charges are used).

| Source | Charge | Level req | Counts? |
|---|---|---|---|
| Rare/magic **staves, Sorceress orbs** ("of Blizzards", "of Meteors") | Blizzard or Meteor | **30** | yes (no level needed for these two) |
| Rare/magic **daggers** (Blizzard, Meteor), **clubs** (Meteor) | Blizzard or Meteor | 60 | yes |
| Moonfall (unique) | level 11 Meteor, 60 charges | 42 | yes |
| Todesfaelle Flamme (unique) | level 10 Fire Wall, 20 charges | 46 | yes (above 3) |
| Nord's Tenderizer (unique) | level 16 Blizzard, 12 charges | 68 | yes |
| Hellrack (unique) | level 18 Immolation Arrow, 150 charges | 76 | yes (above 7) |

- Fire Wall and Immolation Arrow charge suffixes exist in the data but are **set to never spawn**; only the two uniques above have them.
- **Unverified:** a charged skill can only be selected while its item is in the **active weapon set**; swapping may clear the selection. Check in game.
- For speedruns, the most practical is a **level-30 rare/magic staff**, usable by any class. A Sorceress just uses her own skills.
- Data: D2R `MagicSuffix.txt` (`charged` 56 = Meteor, 59 = Blizzard, 51 = Fire Wall, 27 = Immolation Arrow; spawnable/rare columns), `UniqueItems.txt`.
