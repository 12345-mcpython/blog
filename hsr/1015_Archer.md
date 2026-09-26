# Archer

- ID: 1015
- 版本: 联动
- 星级: 5★
- 元素: `量子`
- 命途: 巡猎 (`single`)
- 短名: archer
- 最大能量: 220

> **摘要**：Archer 是 5★ 量子属性、命途「巡猎」角色，联动登场，最大能量 220。内置推荐遗器首推「繁星璀璨的天才」。
>
> 本页数据（技能倍率 / 强化形态 / 星魂 / 行迹 / 忆灵 / 推荐配置 / 培养材料）由游戏解包数据 tbgd 生成；小节内 `ID` 为游戏内配置 ID，便于与原始数据对照。

## 基础面板

| 攻击 | 生命 | 防御 | 速度 | 仇恨 | 暴击率 | 暴击伤害 |
|---|---|---|---|---|---|---|
| 84.48 | 158.4 | 66 | 105 | 75 | 0.05 | 0.5 |

## 面板成长

> 数据源 `AvatarPromotionConfig`。表中生命/攻击/防御为该**晋阶的基础值**（配置原值，不含光锥与遗器）；最后一行按模拟器倍率公式 `1 + (等级−1)×0.05 + 晋阶数×0.4` 推算满级面板。

| 晋阶 | 等级上限 | 生命 | 攻击 | 防御 | 速度 | 晋升消耗 |
|---|---|---|---|---|---|---|
| 0 | 20 | 158.4 | 84.48 | 66 | 105 | 信用点×4000 · 熄灭原核×5 |
| 1 | 30 | 221.76 | 118.272 | 92.4 | 105 | 信用点×8000 · 熄灭原核×10 |
| 2 | 40 | 285.12 | 152.064 | 118.8 | 105 | 信用点×16000 · 微光原核×6 · 暗帷月华×3 |
| 3 | 50 | 348.48 | 185.856 | 145.2 | 105 | 信用点×40000 · 微光原核×9 · 暗帷月华×7 |
| 4 | 60 | 411.84 | 219.648 | 171.6 | 105 | 信用点×80000 · 蠢动原核×6 · 暗帷月华×20 |
| 5 | 70 | 475.2 | 253.44 | 198 | 105 | 信用点×160000 · 蠢动原核×9 · 暗帷月华×35 |
| 6 | 80 | 538.56 | 287.232 | 224.4 | 105 | — |
| **满级 Lv80（已晋阶）** | 80 | 1164.24 | 620.93 | 485.1 | 105 | — |

## 技能
### 普攻：干将•莫邪 / Kanshou and Bakuya

- 攻击类型: 普攻 · 效果: `单体攻击` · 元素: `量子` · 最高等级: 9
- 破韧值: 单体 30, 全体 0, 扩散 0

> 对指定敌方单体造成等同于Archer<strong>100%</strong>攻击力的量子属性伤害。
>
> *Deals Quantum DMG equal to <strong>100%</strong> of Archer's ATK to one designated enemy.*

<details><summary>参数（全部等级）</summary>

| 等级 | 参数 |
|---|---|
| 1 | [0.5] |
| 2 | [0.6] |
| 3 | [0.7] |
| 4 | [0.8] |
| 5 | [0.9] |
| 6 | [1] |
| 7 | [1.1] |
| 8 | [1.2] |
| 9 | [1.3] |

</details>

<details><summary>分段：Avatar_Archer_00_Skill01_Phase02（单目标 3 段 · 主目标）</summary>

- 单目标段数: **3** 段（主目标） · 全部段实例数: 3 · 段数合计(含变体): 3
- 分裂比合计: 1.0

| # | 目标 | 伤害公式 | 分裂比 | 单目标段数 | 实例数 | 备注 |
|---|---|---|---|---|---|---|
| 1 | 主目标 | `P#1` | 0.3 | 1 | 1 | — |
| 2 | 主目标 | `P#1` | 0.35 | 1 | 1 | — |
| 3 | 主目标 | `P#1` | 0.35 | 1 | 1 | — |

</details>

### 战技：伪•螺旋剑 / Caladbolg II: Fake Spiral Sword

- 攻击类型: 战技 · 效果: `单体攻击` · 元素: `量子` · 最高等级: 15
- 破韧值: 单体 60, 全体 0, 扩散 0

> 进入【回路连接】状态。对指定敌方单体造成等同于Archer<strong>360%</strong>攻击力的量子属性伤害。【回路连接】状态下施放战技后，本回合不会结束，并使Archer战技造成的伤害提高<strong>100%</strong>，该效果可以叠加<strong>2</strong>层，持续至退出【回路连接】状态。主动施放<strong>5</strong>次战技后或战技点不足以再次施放战技则退出【回路连接】状态。每个波次中的所有敌方目标被消灭后会退出【回路连接】状态。
>
> *Enters the "Circuit Connection" state. Deals Quantum DMG equal to <strong>360%</strong> of Archer's ATK to one designated enemy. After using Skill in the "Circuit Connection" state, the current turn does not end, and the DMG dealt by Archer's Skill increases by <strong>100%</strong>. This effect can stack up to <strong>2</strong> time(s), lasting until he exits the "Circuit Connection" state. After actively using Skill <strong>5</strong> time(s) or when Skill Points are insufficient to use Skill again, exits the "Circuit Connection" state. After all enemy targets have been defeated in each wave, exits the "Circuit Connection" state.*

<details><summary>参数（全部等级）</summary>

| 等级 | 参数 |
|---|---|
| 1 | [1.8, 0.6, 2, 1, 5] |
| 2 | [1.98, 0.64, 2, 1, 5] |
| 3 | [2.16, 0.68, 2, 1, 5] |
| 4 | [2.34, 0.72, 2, 1, 5] |
| 5 | [2.52, 0.76, 2, 1, 5] |
| 6 | [2.7, 0.8, 2, 1, 5] |
| 7 | [2.925, 0.85, 2, 1, 5] |
| 8 | [3.15, 0.9, 2, 1, 5] |
| 9 | [3.375, 0.95, 2, 1, 5] |
| 10 | [3.6, 1, 2, 1, 5] |
| 11 | [3.78, 1.04, 2, 1, 5] |
| 12 | [3.96, 1.08, 2, 1, 5] |
| 13 | [4.14, 1.12, 2, 1, 5] |
| 14 | [4.32, 1.16, 2, 1, 5] |
| 15 | [4.5, 1.2, 2, 1, 5] |

</details>

<details><summary>分段：Avatar_Archer_00_Skill02_Phase02（单目标 1 段 · 主目标）</summary>

- 单目标段数: **1** 段（主目标） · 全部段实例数: 1 · 段数合计(含变体): 1

| # | 目标 | 伤害公式 | 分裂比 | 单目标段数 | 实例数 | 备注 |
|---|---|---|---|---|---|---|
| 1 | 主目标 | `(P#1 * Dyn1)` | — | 1 | 1 | — |

</details>

### 终结技：无限剑制 / Unlimited Blade Works

- 攻击类型: 终结技 · 效果: `单体攻击` · 元素: `量子` · 最高等级: 15
- 破韧值: 单体 90, 全体 0, 扩散 0
- 释放所需能量: 220

> 对指定敌方单体造成等同于Archer<strong>1000%</strong>攻击力的量子属性伤害，并获得<strong>2</strong>点充能，最多可拥有<strong>4</strong>点充能。
>
> *Deals Quantum DMG equal to <strong>1000%</strong> of Archer's ATK to one designated enemy and gains <strong>2</strong> point(s) of Charge, up to a maximum of <strong>4</strong>.*

<details><summary>参数（全部等级）</summary>

| 等级 | 参数 |
|---|---|
| 1 | [6, 2, 4] |
| 2 | [6.4, 2, 4] |
| 3 | [6.8, 2, 4] |
| 4 | [7.2, 2, 4] |
| 5 | [7.6, 2, 4] |
| 6 | [8, 2, 4] |
| 7 | [8.5, 2, 4] |
| 8 | [9, 2, 4] |
| 9 | [9.5, 2, 4] |
| 10 | [10, 2, 4] |
| 11 | [10.4, 2, 4] |
| 12 | [10.8, 2, 4] |
| 13 | [11.2, 2, 4] |
| 14 | [11.6, 2, 4] |
| 15 | [12, 2, 4] |

</details>

<details><summary>分段：Avatar_Archer_00_Skill03_Phase02（单目标 15 段 · 主目标）</summary>

- 单目标段数: **15** 段（主目标） · 全部段实例数: 15 · 段数合计(含变体): 15
- 分裂比合计: 1.0

| # | 目标 | 伤害公式 | 分裂比 | 单目标段数 | 实例数 | 备注 |
|---|---|---|---|---|---|---|
| 1 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 2 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 3 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 4 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 5 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 6 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 7 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 8 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 9 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 10 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 11 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 12 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 13 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 14 | 主目标 | `P#1` | 0.04 | 1 | 1 | — |
| 15 | 主目标 | `P#1` | 0.44 | 1 | 1 | — |

</details>

### 天赋：心眼（真） / Mind's Eye (True)

- 攻击类型: 无 · 效果: `单体攻击` · 元素: `量子` · 最高等级: 15
- 破韧值: 单体 30, 全体 0, 扩散 0

> 当Archer的队友对敌方目标施放攻击后，Archer消耗1点充能，立即对主目标发动<u>追加攻击</u>，造成等同于Archer<strong>200%</strong>攻击力的量子属性伤害，并恢复1个战技点。若本次<u>追加攻击</u>施放前目标被消灭则对敌方随机单体发动<u>追加攻击</u>。
>
> *After Archer's teammates attack enemy targets, Archer consumes 1 Charge and immediately launches <u>Follow-Up ATK</u> on the primary target, dealing Quantum DMG equal to <strong>200%</strong> of Archer's ATK and recovering 1 Skill Point. If the target is defeated before this <u>Follow-Up ATK</u> is launched, the <u>Follow-Up ATK</u> will be directed at one random enemy instead.*

- **追加攻击**：满足条件时自动触发的效果，对目标施放1次额外的攻击。

<details><summary>参数（全部等级）</summary>

| 等级 | 参数 |
|---|---|
| 1 | [1] |
| 2 | [1.1] |
| 3 | [1.2] |
| 4 | [1.3] |
| 5 | [1.4] |
| 6 | [1.5] |
| 7 | [1.625] |
| 8 | [1.75] |
| 9 | [1.875] |
| 10 | [2] |
| 11 | [2.1] |
| 12 | [2.2] |
| 13 | [2.3] |
| 14 | [2.4] |
| 15 | [2.5] |

</details>

### 地图攻击：攻击 / Attack

- 攻击类型: 地图普攻 · 效果: `地图攻击` · 元素: `量子` · 最高等级: 1
- 破韧值: 单体 30, 全体 0, 扩散 0

> 攻击敌人，进入战斗后削弱敌方目标对应属性韧性。
>
> *Attacks an enemy, and after entering combat, reduces their Toughness of the corresponding Type.*

<details><summary>参数（全部等级）</summary>

| 等级 | 参数 |
|---|---|
| 1 | [] |

</details>

### 秘技：千里眼 / Clairvoyance

- 攻击类型: 秘技 · 效果: `地图攻击` · 元素: `量子` · 最高等级: 1
- 破韧值: 单体 60, 全体 0, 扩散 0

> 立即攻击敌人，进入战斗后对敌方全体造成等同于Archer<strong>200%</strong>攻击力的量子属性伤害，并获得<strong>1</strong>点充能。
>
> *Immediately attacks enemies. After entering combat, deals Quantum DMG equal to <strong>200%</strong> of Archer's ATK to all enemies and gains <strong>1</strong> point(s) of Charge.*

<details><summary>参数（全部等级）</summary>

| 等级 | 参数 |
|---|---|
| 1 | [2, 1] |

</details>

### 强化战技：结束 / End

- 攻击类型: 战技 · 效果: `强化` · 元素: `无` · 最高等级: 1
- 破韧值: 单体 0, 全体 0, 扩散 0

> 退出【回路连接】状态，并结束回合。
>
> *Exits "Circuit Connection" state and ends the turn.*

<details><summary>参数（全部等级）</summary>

| 等级 | 参数 |
|---|---|
| 1 | [] |

</details>

## 行迹
- 投影魔术：Archer在场时，战技点上限额外增加<strong>2</strong>点。　(`1015101`)
- 正义伙伴：Archer进入战斗时获得<strong>1</strong>点充能。　(`1015102`)
- 守护者：我方获得战技点后，若战技点大于等于<strong>4</strong>点，Archer的暴击伤害提高<strong>120%</strong>，持续<strong>1</strong>回合。　(`1015103`)
- **量子属性伤害提高(quantum_damage_boost)** + 3.20%　(`1015201`)
- **攻击力(attack_percent)** + 4.00%　(`1015202`)
- **量子属性伤害提高(quantum_damage_boost)** + 3.20%　(`1015203`)
- **暴击率(crit_chance)** + 2.70%　(`1015204`)
- **量子属性伤害提高(quantum_damage_boost)** + 4.80%　(`1015205`)
- **攻击力(attack_percent)** + 6.00%　(`1015206`)
- **量子属性伤害提高(quantum_damage_boost)** + 4.80%　(`1015207`)
- **暴击率(crit_chance)** + 4.00%　(`1015208`)
- **攻击力(attack_percent)** + 8.00%　(`1015209`)
- **量子属性伤害提高(quantum_damage_boost)** + 6.40%　(`1015210`)

## 星魂
### 星魂 1：未曾触及的理想 / The Unreached Dream

> 单个回合内施放<strong>3</strong>次战技后，为我方恢复<strong>2</strong>个战技点。

- 参数: [3, 2]

### 星魂 2：未能实现的幸福 / The Unfulfilled Happiness

> 施放终结技时，使敌方目标的量子属性的抗性降低<strong>20%</strong>，并为其添加量子属性弱点，持续<strong>2</strong>回合。

- 参数: [0.2, 2]

### 星魂 3：难为凡庸的意气 / The Untamed Will

> 战技等级+2，最多不超过15级；普攻等级+1，最多不超过10级。
- 技能等级+`{'101502': 2, '101501': 1}`

### 星魂 4：难称英雄的一生 / The Unsung Life

> 造成的终结技伤害提高<strong>150%</strong>。

- 参数: [1.5]

### 星魂 5：无铭孤影的守望 / The Nameless Watch

> 终结技等级+2，最多不超过15级；天赋等级+2，最多不超过15级。
- 技能等级+`{'101503': 2, '101504': 2}`

### 星魂 6：无尽徘徊的巡礼 / The Endless Pilgrimage

> 回合开始时为我方恢复1个战技点。自身战技提供的伤害提高效果可以叠加的上限层数提高<strong>1</strong>层。造成的战技伤害无视<strong>20%</strong>的防御力。

- 参数: [1, 0.2]

## 推荐配置

> 游戏内置推荐（`AvatarEquipRecommend` / `AvatarRelicRecommend`），非社区攻略；
> 光锥详见 [光锥预览](/hsr/WEAPONS.md)，套装效果详见 [遗器套装](/hsr/RELICS.md)。

- 推荐光锥: 5★理想燃烧的地狱 / 5★星海巡航
- 推荐遗器（4 件套）: 繁星璀璨的天才 / 识海迷坠的学者 / 野穗伴行的快枪手
- 推荐饰品（2 件套）: 繁星竞技场 / 太空封印站 / 停转的萨尔索图
- 推荐主词条: 躯干 暴击率 · 脚部 攻击力 · 位面球 量子属性伤害提高 · 连结绳 攻击力
- 主词条备选: 躯干 暴击率/暴击伤害；脚部 攻击力/速度；位面球 量子属性伤害提高/攻击力；连结绳 攻击力/能量恢复效率
- 副词条优先级: 暴击率 > 暴击伤害 > 攻击力 > 速度

## 培养材料

> 游戏内置养成消耗（`AvatarUseMaterialData` + `ItemConfig`）。

| 用途 | 材料 | 稀有度 |
|---|---|---|
| 晋阶材料 | 暗帷月华 | ★★★★ |
| 晋阶首领材料 | 守护者的悲愿 | ★★★★ |
| 行迹材料·小 | 陨铁弹丸 | ★★ |
| 行迹材料·中 | 命定死因 | ★★★ |
| 行迹材料·大 | 逆时一击 | ★★★★ |
| 世界掉落·小 | 熄灭原核 | ★★ |
| 世界掉落·中 | 微光原核 | ★★★ |
| 世界掉落·大 | 蠢动原核 | ★★★★ |

## 常见问题

- **Archer的基础面板是多少？** Lv1：攻击 84.48、生命 158.4、防御 66、速度 105、暴击率 0.05、暴击伤害 0.5；各晋阶基础值与 Lv80 满级面板见上文「面板成长」。
- **Archer用什么遗器？** 4 件套推荐 繁星璀璨的天才 / 识海迷坠的学者 / 野穗伴行的快枪手；2 件套（位面饰品）推荐 繁星竞技场 / 太空封印站 / 停转的萨尔索图。
- **Archer的遗器主词条怎么选？** 躯干暴击率、脚部攻击力、位面球量子属性伤害提高、连结绳攻击力。
- **Archer的副词条优先级？** 暴击率 > 暴击伤害 > 攻击力 > 速度。
- **Archer用什么光锥？** 内置推荐 5★理想燃烧的地狱 / 5★星海巡航。
- **Archer需要哪些培养材料？** 晋阶材料 暗帷月华、晋阶首领材料 守护者的悲愿；行迹材料 陨铁弹丸 / 命定死因 / 逆时一击；世界掉落 熄灭原核 / 微光原核 / 蠢动原核。
- **Archer有仓库技吗？** 没有。该角色未列入 `AvatarGlobalBuffConfig` 全局辅助技能表。
- **Archer是加强角色吗？** 不是。该角色未列入角色加强名单。
