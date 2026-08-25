# L4D2 Versus Single — Zonemod-like

**[English](README.md) | [中文](README_zh.md)**

基于 [Versus Single](https://steamcommunity.com/sharedfiles/filedetails/?id=186808048)（作者 def075，创意工坊 ID：186808048）的修改版，重构为 **ZoneMod 风格计分**、**路程分与官方计分板对齐**、**武器数据重平衡**。

1 名真人玩家作为 4 人幸存者小队中的一员（其余 3 名为 AI bots），特殊感染者全部由 AI 指挥——单人即可体验完整对抗模式的攻防节奏。

---

## 功能特性

### ZoneMod 风格计分

```
总分 = 路程分 + (HealthBonus + DamageBonus) × 存活倍率
```

- **存活倍率** = 当前存活幸存者数 ÷ 4（人越少，奖励折算越低）
- 总分实时写入引擎计分变量 `vs_survival_bonus`（引擎结算分 = cvar × 存活人数）

### 路程分与官方计分板对齐

- **途中**：`Dist = 官方进度百分比 × 该图满分`——普通关卡与 TAB 计分板完全一致
- **通关结算**：通关瞬间 Dist 强制 = 该图满分（灌油关 / 守点关 / 普通关通用）
- 内置满分表：**57 张官方图**（c1-c14）+ **31 个创意工坊 VPK**（约 135 张图，取自各图 mission 的 `VersusCompletionScore`；无官方满分值的图默认 500）

### DamageBonus 扣分规则

| 情况 | 扣减 |
|---|---|
| 受伤后实血只剩 1（黑血状态） | 该次伤害 × 1/4 |
| 每次被击倒（含第 1 次） | -12% |
| 击倒后未被救起而死亡 | -12% |
| 第 3 次击倒直接死亡 | -12% |
| 倒地状态中受击 | 不扣 |

- 每回合开始重置回 100%

### 武器数据重平衡

| 武器 | 伤害 | 弹丸数 | 单发总伤 | 伤害衰减 | 垂直 / 水平散布 | 换弹时间 |
|---|---|---|---|---|---|---|
| 泵动霰弹枪（Pump Shotgun） | 16/弹丸 | 17 | **272** | 0.7 | 3 / 5 | 0.473s/发 |
| 铬色霰弹枪（Chrome Shotgun） | 28/弹丸 | 9 | **252** | 0.65 | 4.5 / 5.5 | 0.473s/发 |
| 冲锋枪（SMG） | 22 | 1 | 22 | 0.81 | 连发散布 0.22，移动最大 2 | 1.9s |
| 消音冲锋枪（Silenced SMG） | 25 | 1 | 25 | 0.81 | 连发散布 0.25，移动最大 2.35 | 2.24s |

### 聊天栏实时输出

- **每 5 秒**在聊天栏输出一行（无 "Console:" 前缀）：

  ```
  [VS Single] Dist:xxx HB:xxx<血量%> DB:xxx<剩余%> Bonus:xxx
  ```

- **通关瞬间**立即输出最终汇总（Dist = 图满分），通关后停止每 5 秒刷新，汇总不被覆盖

### 其他

- **Tank**：3000 血量（vs 模式引擎 ×1.5 后实际 4500），出现率 100%（路程 15%~90% 区间）
- **Witch**：关闭
- 在每个关卡的开头给予每名生还者一瓶止痛药
- 特感：幽灵延迟 1 秒，Hunter / Smoker 各限 1 只；尸潮每次 25 只、间隔 100 秒（事实上某些地图的特定区域会同时刷新不止1只Hunter或Smoker）
- 每回合最多 99 次换队（`vs_max_team_switches 99`）

---

## 文件结构

```
├── addoninfo.txt              # 附加内容信息（标题、作者、版本）
├── modes/
│   └── versus_single.txt      # 游戏模式定义（base "versus" + cvar 配置）
├── scripts/
│   ├── vscripts/
│   │   └── versus_single.nut  # 全部 mod 逻辑（Squirrel vscript）
│   └── weapons/
│       ├── weapon_pumpshotgun.txt
│       ├── weapon_shotgun_chrome.txt
│       ├── weapon_smg.txt
│       └── weapon_smg_silenced.txt
└── README.md / README_zh.md
```

---

## 已适配的创意工坊地图（31 个）

`dark carnival remix` · `snow_town` · `dead_center_2025` · `dead center rebirth fixed` · `parish overgrowth` · `noecho` · `outline` · `nomercyrehab` · `ccrerouted` · `dead center reconstructed` · `dead_air_redux_aw` · `deadbeforedawn2_dc` · `daybreak_v3` · `ihatemountains2` · `suicideblitz2` · `cmpn_FatalFreightFix` · `energycrisis` · `downpour` · `deathsentence` · `tourofterror` · `deadbeatescape` · `hauntedforest_v3` · `bloodtracks` · `detourahead` · `city17l4d2` · `l4d2_diescraper_362` · `carriedoff` · `openroad` · `tripday` · `undead_zone` · `deathaboard2`

各图满分取自其 mission 文件的 `VersusCompletionScore`（该图官方计分板路程分上限）；无官方满分值的 16 张图（如 tourofterror 全部）默认 **500**。

---

## 已知问题

- **finale 关途中路程分**（灌油 / 守点关）：官方路程分包含任务进度（灌油桶数 / 防守波次），vscript 无法读取——途中输出为位置百分比对应值。**通关结算正确**（强制 = 图满分）
- **屏幕 HUD 无法显示**：L4D2 的所有屏幕文字通道损坏（引擎 splitscreen bug）——所有输出走**聊天栏**
- 地图名大小写自适应匹配（部分创意工坊 mission 的 Map 字段为混合大小写，与 bsp 文件名的全小写不同）

---

## 致谢

- 原版 mod：**Versus Single**，作者 **def075**（创意工坊 ID 186808048）
- 计分系统参考自[Zonemod Docs - Sirplease](https://sirplease.net/docs/modes/zonemod#general)
- 武器重平衡数据参考自[Zonemod Docs - Sirplease](https://sirplease.net/docs/modes/zonemod#items)
- 本次重构完全由 **deepseek-v4-flash-0731** 完成——功劳应有它一份
