# 永久重生怪：被凍結的重生怪為什麼不會消失

> **重點：** 死靈法師的重生怪本來會在 **180 秒**後死掉，遊戲是用一個**一次性的計時事件**來殺掉牠的。
> 如果計時事件觸發的那一刻，重生怪剛好處於**凍結**狀態，遊戲就會**把這個事件丟掉，而且不再重新排程**，重生怪就會**一直存在**（直到牠被打死，或你離開遊戲）。
> 最容易取得的凍結來源是第五幕的**刨肉薩滿**：牠的冰系法術是冰尖柱，會**凍結**目標。

---

## 1. 重生怪平常怎麼到期

- 施放**重生**時（`SKILLS_SrvDo058_Revive`，`D2Game/src/SKILLS/SkillNec.cpp:1490`），遊戲會排一個 `EVENTTYPE_MONUMOD` 類型的**一次性事件**，時間是**重生時間 + 持續時間**（`SkillNec.cpp:1621`）。
- **持續時間**：D2R `skills.txt` 裡重生的 `Param3` = **4500 幀 = 180 秒**，不會隨技能等級增加。
- 事件觸發時，遊戲會執行「暫時召喚物」詞綴的處理函式（`sub_6FC6DFC0`，`D2Game/src/MONSTER/MonsterUnique.cpp:1750-1774`）。它會：
  - 如果重生怪已經死了，就什麼都不做；
  - 如果重生怪處於**不可打斷**狀態（正在放技能），就在 3 幀後重試；
  - 否則就**殺掉重生怪**（從死靈法師身上移除）。
- 沒有任何其他地方會重新排這個事件。**它只會觸發一次。**

---

## 2. 為什麼凍結能擋下來

怪物的事件分派函式 `MONSTERMODE_EventHandler`（`D2Game/src/MONSTER/MonsterMode.cpp:2130-2150`）有這樣一條規則：

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
        return;   // 事件被丟掉：不執行，也不重新排程
    }
    break;
}
```

- 怪物處於**凍結**狀態（`STATE_FREEZE`）而且還活著時，**除了上面列出的五種，其他事件全部被丟掉**。
- 重生怪的到期事件 `EVENTTYPE_MONUMOD` **不在名單裡**。
- 所以重生怪如果**在 180 秒剛好到的那一幀被凍住**，到期事件就被丟掉了。之後沒有任何到期機制，**重生怪就不會因為時間到而死掉**。
- 之後重生怪一切正常。凍結結束時，`SUNITDMG_RemoveFreezeState`（`D2Game/src/UNIT/SUnitDmg.cpp:102`）會重新啟動牠的 AI。

---

## 3. 凍結和冰緩不同：一定要真正的凍結

這個漏洞需要的是 **`STATE_FREEZE`（凍結）**。大部分冰系攻擊只會**冰緩**（`STATE_COLD`），冰緩**沒有用**。

在 D2R 的資料裡，攻擊會真正**凍結**的怪物只有這些：

| 怪物 | 凍結來源 | 出現位置 |
|---|---|---|
| **刨肉薩滿**（`fallenshaman6`）⭐ | 技能 **ShamanIce**，射出**冰尖柱**（`glacialspike`，命中時凍結） | **亞瑞特高原**、**先祖之路**（第五幕，**惡夢和地獄**） |
| 召喚者 | 冰尖柱 | 神秘避難所 |
| 迪亞布羅 | DiabCold | 混沌避難所 |
| 巴爾 | 第二種近戰攻擊（凍結屬性） | 世界之石大殿 |

**刨肉薩滿**是最實際的選擇：牠是第五幕一般區域的普通怪物，而且會一直放冰系法術。

### 什麼樣的怪會被凍結
`SUNITDMG_ApplyFreezeState`（`SUnitDmg.cpp:1645-1725`）只會凍結符合以下條件的怪物：
- 當下**不是不可打斷**狀態；
- **不是頭目或傭兵**（這兩種只會被冰緩）；
- `monstats.txt` 裡當前難度的 **`coldeffect` 是負數**。大部分能被重生的怪物都符合。

怪物被凍結的時間會**依難度除以一個係數**（`DifficultyLevels.txt` 的 `MonsterFreezeDivisor`）：**一般 ÷1、惡夢 ÷2、地獄 ÷4**。難度越高，窗口越短。

---

## 4. 怎麼做

1. **重生一隻會被凍結的怪**（`coldeffect` 是負數），記下**重生的時間**。
2. 在**惡夢或地獄**難度去**亞瑞特高原**或**先祖之路**，找到**刨肉薩滿**。
3. 在**重生後大約 2:55–3:00**，讓重生怪**跟薩滿打起來**，被牠們的**冰尖柱**打中。
4. 重生怪必須在 **180 秒那一刻是凍住的**（從施放重生起算 4500 幀）。
5. 如果過了 3:00 幾秒重生怪還活著，就是成功了，牠**不會再因時間到而消失**。

**小技巧：**
- 一次重生好幾隻。牠們大約同時到期，同一隻薩滿的冰尖柱就有機會一次凍住好幾隻。
- 被凍結的怪物會多受傷害，而且可能被打碎，所以不要拿你最珍貴的重生怪來練習。
- 重生怪不會被存檔，所以永久重生怪會一直存在到**牠死掉或你離開遊戲**為止。

---

## 5. 參考資料

| 常數 | 數值 |
|---|---|
| 重生持續時間 | 4500 幀 = **180 秒**（`skills.txt` 重生的 `Param3`） |
| 到期事件 | `EVENTTYPE_MONUMOD`，**只觸發一次** |
| 凍結時仍會執行的事件 | `STATREGEN`、`TRAP`、`RESET`、`PERIODICSKILLS`、`REMOVESTATE` |
| 怪物凍結時間的除數 | 一般 1 / 惡夢 2 / 地獄 4 |
| ShamanIce 凍結時間（`skills.txt`） | `ELen` 125 幀，每級 +50，再除以上面的除數 |
| 伺服器幀率 | 每秒 25 幀 |

### 原始碼對照（D2MOO，《暗黑破壞神 II》1.10 伺服器程式碼）

| 內容 | 位置 |
|---|---|
| 重生和它的到期事件 | `D2Game/src/SKILLS/SkillNec.cpp` → `SKILLS_SrvDo058_Revive` |
| 到期處理（殺掉重生怪） | `D2Game/src/MONSTER/MonsterUnique.cpp` → `sub_6FC6DFC0` |
| 凍結的單位會丟掉事件 | `D2Game/src/MONSTER/MonsterMode.cpp` → `MONSTERMODE_EventHandler` |
| 誰會被凍結，以及除數 | `D2Game/src/UNIT/SUnitDmg.cpp` → `SUNITDMG_ApplyFreezeState` |
| 凍結結束時重新啟動 AI | `D2Game/src/UNIT/SUnitDmg.cpp` → `SUNITDMG_RemoveFreezeState` |
| 冰尖柱命中時凍結 | `D2Game/src/MISSILES/MissMode.cpp` → `MISSMODE_SrvHit13_GlacialSpike_HellMeteorDown` |

---

## 6. 注意事項

- **程式碼版本：** 這套邏輯來自 **D2MOO**，也就是開源重建的**《暗黑破壞神 II》1.10** 伺服器程式碼，不是 D2R 本身的程式。D2R 3.3.94012 的資料在關鍵數值上都一致（重生 180 秒、刨肉薩滿的 ShamanIce 冰尖柱、凍結除數），而且這招在 D2R 據說有效，但確切行為沒有經過證實。
- **ShamanIce 在薩滿實際技能等級下的凍結時間**沒有算出來，所以各難度的窗口大小只是大概。
