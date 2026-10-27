# 接入包：骏大祭（matsuri）

> S0 产出。用途：把零散情报收成**可核对的结构**，一次分组确认，减少来回追问。
> 填完后：机制结论落 [`mechanism.md`](../mechanism.md)，术语 / 口径落 [`glossary.md`](../glossary.md)，
> 数值占位落 [`stubs.md`](stubs.md)。
> 规则：**机制未确认 = 阻塞**；**数值待确认 = 可不阻塞**（用占位并登记）。来源必须标可信度。

## 1. 情报位置清单

| 位置 | 可能内容 | 获取方式 | 可信度 | 状态 |
|---|---|---|---|---|
| 官方公告 / 官网 | 剧本名、实装日期、机制概览 | 官网 / 官方社媒 | 高 | ☐ |
| 游戏内公告 / 帮助 | 机制说明、规则文本、活动页 | 游戏内截图 / 录屏 | 高 | ☐ |
| 剧本数据表（master.mdb） | 日程、演出 / 传授 / 融合 / 结算等表名与数值 | `master.mdb` sqlite（`single_mode_15_*`） | 高（静态数据） | ◐ 已提取结构 |
| 真机回合帧 | 每回合状态、候选动作、事件、结算 | `.portable` 帧通道 / 分析器输出 | 最高（行为事实） | ☐ 未接入 |
| 真机终局帧 | 终局评分、PT、五维、剧本资源 | `.portable` 终局帧 | 最高 | ☐ 未接入 |
| 旧仓实现 | 同剧本旧实现 | 无（本剧本为新剧本） | — | 不适用 |
| 日文调查备忘 | 机制原文、术语、公式线索 | mee-umasim `data/shuntaisai_memo.md` | 中（作者自述不可信、持续更新） | ◐ 已参考 |
| 攻略 / 社区 / 视频 | 机制流程、数值区间、注意事项 | 4Gamer / Famitsu 先行体验 | 中高（媒体实测） | ◐ 已参考 |
| 引擎现有文档 | 通用机制、术语、固有效果、预告情报 | 引擎 `docs/game/`、`docs/notes/matsuri_scenario_preview.md` | 高（通用）/ 中（预告） | ◐ |
| 用户口述 / 实测 | 机制细节、数值、交互 | 对话 / 截图 / 录像 | 中高（需记录原话） | ◐ |

- 同一事实多处冲突时：**真机 > 官方 > 旧仓 / 日文备忘 > 社区 / 口述**；分歧写入 `glossary.md` 决定记录。
- 文件类情报记录**相对路径或来源标识**，不放绝对路径。

## 2. 机制清单

| 机制点 | 规则 / 流程 | 触发条件 | 结算顺序 | 交互 | 状态 | 来源 | 影响功能 |
|---|---|---|---|---|---|---|---|
| 演目 ↔ 训练 | 流鏑馬=速 / 神旗争奪戦=耐 / 納舞=力 / 大曳神輿=根 / 飛駆=智 | 全局 | — | 自动 | 已确认 | official / data | 训练、gauge |
| 演目担当（编成内） | 由编成支援卡类型决定担当演目（速卡→流鏑馬） | 建局 | — | 自动 | 已确认 | official / memo | 人头、gauge |
| 演目担当（编成外） | 固定 5 名神駒，每演目 1 人；不会出现 0 人 | 建局 / 第 3 回合加入 | — | 自动 | 已确认 | official / data | 人头、事件 |
| 演目ゲージ | 对应担当参加训练 +1；友情 +2；休息 / 非目标比赛最低 +1；不自然衰减 | 训练 / 休息 / 比赛后 | 动作结算后 | 自动 | 部分确认（❓归属） | official / memo | 资源账本 |
| 演目稽古 | gauge 满 → 对应训练变为演目稽古；执行后 Lv +1、gauge 清零；可延期、多亮并存 | gauge 满 | 训练决策点 | 玩家选择 | 已确认 | official / memo | 动作集、训练值 |
| 演目 Lv | 演目稽古 +1；上限 6，SSR 吾妻 10；Lv8 后增幅大 | 演目稽古 | 结算后 | 自动 | 上限已确认 / 数值待确认 | official / data | 训练值、教え |
| 教え（伝受） | 第 3 回合、Junior 年末、Classic 年末共 3 次；每次 5 个教导分配到 5 演目 | 固定回合 | 回合末尾 | 拖拽分配 | 已确认（时点） | official / data | 事件、面板 |
| 教え（転成） | 初始 5 种之一作材料，强化新教え；新教え保留自身效果 + 転成效果；组合不可改 | 進捗発表会后 | 伝受后 | 选择 | 基本确认（❓保留写法） | official / memo / data | 教え槽位 |
| 進捗発表会 | 检查全部演目 Lv；Junior ≥3 / Classic ≥5 / Senior 本番 ≥6；成功 / 大成功 | 年末 / Senior 10 月後半 | 回合末尾 | 自动 | 门槛已确认 | official / data | 结算、buff |
| 午式駿大祭本番 | Senior 10 月後半最终结算；之后剧情结束但仍可攒 gauge / 演目稽古到 turn 78 | turn 68 | 剧本结算 | 自动 | 部分确认（❓finals act） | official / memo / data | 结算、终局 |
| 合宿 | 共 3 次，Junior 即有（7 月前半–8 月後半）；全部训练变演目稽古；不回体；目标赛延长 | 合宿回合 | 回合开始 | 自动 | 部分确认（❓延长量） | official / memo | 阶段、训练 |
| 友人「吾妻」 | SSR / R / 不带分别 Lv 上限 10 / 6 / 6；出行最低演目 +1；4 人以下稽古补位 | 编成 / 出行 | 出行结算 | 自动 | 部分确认（❓分身 / 疲劳） | official / memo / data | 人头、资源 |
| 编成门禁 | 第三年部分演目稽古 buff 需支援卡类型 ≥5 种 | 第三年 | — | 自动 | 已确认（❓是否含友人） | official / memo | 组卡、训练值 |
| 属性上限 | 基础 2200 / 1850 / 1800 / 1700 / 1800；绝对上限 2500 | 全局 | — | 自动 | 已确认（上限） | official / data | 训练、评分 |

## 3. 数值清单

| 项 | 当前值 | 来源 | 状态 | 落点（数据 / 配置 / 常量） |
|---|---|---|---|---|
| 回合总数 | 0..77（78 回合） | official / memo | 已确认 | `MAX_TURN = 77` |
| 進捗発表会回合 | turn 24 / 48 / 68 | data `single_mode_15_schedule` | 已确认 | 剧本资产 / 常量 |
| 進捗発表会门槛 | 全演目 Lv ≥3 / ≥5 / ≥6 | data `single_mode_15_schedule` | 已确认 | 剧本资产 / 常量 |
| 属性基础上限 | 2200 / 1850 / 1800 / 1700 / 1800 | official | 已确认 | 协议 `fiveStatusLimit` / 剧本侧 |
| 属性绝对上限 | 2500 ×5 | data `single_mode_scenario` row 15 / official | 已确认 | 剧本侧上调 |
| 演目 Lv 上限 | SSR 友人 10；R / 不带 6 | official / data `outing_effect` | 已确认 | 剧本资产 |
| 演目稽古 Lv1 | 全五项 +10 以上 + SP | official / memo | 数值待确认 | `training_basic_value`（S2） |
| 教え效果 | `advice_effect` 10 种 × Lv1–10（153 行） | data | 待映射 | 剧本资产（S2） |
| 転成效果 | `fusion_effect` 9 行（advice 101/201/301/401/501） | data | 待映射 | 剧本资产（S2） |
| 進捗発表会效果 | `check_point_effect` 20 行（3 schedule × result state） | data | 待映射 | 剧本资产（S2） |
| finals act | `finals_act_effect` 4 行 | data | 待映射 | 剧本资产（S2） |
| 出行效果 | `outing_effect` 10 行（卡 30323 / 10149 × story_step 1–5，add_level 1） | data | 已确认结构 | 剧本资产 |
| 训练基础值 | 模板样例值（STUB） | 模板 | 数值待确认 | `scenario_matsuri.json` |
| 组卡门禁 | 类型 ≥5 种（是否含友人待确认） | official / memo | 部分确认 | `DeckSpec.min_types`（剧本侧） |

## 4. 数据表清单

> 来自 `master.mdb` 的 `single_mode_15_*`，共 11 张；列为实际字段，行数为当前数据量。

| 表名 | 字段 / 形态 | 行数 | 状态 | 备注 |
|---|---|---|---|---|
| `single_mode_15_act_effect` | `id, level, condition_type, condition_value, effect_type, effect_value_1..4` | 66 | 已有 | Lv1–10，每级多条效果 |
| `single_mode_15_act_info` | `act_type, command_id` | 5 | 已有 | act 1–5 → command 5101–5105 |
| `single_mode_15_act_member` | `id, act_type, chara_id, priority` | 5 | 已有 | 编成外 5 名神駒，priority 100 |
| `single_mode_15_advice` | `id, fusion_type, add_schedule_type` | 10 | 已有 | 初始 101–501；追加 601–1001 |
| `single_mode_15_advice_effect` | `id, advice_id, level, condition_type, condition_value, effect_type, effect_value_1..4` | 153 | 已有 | 10 种 × Lv1–10 |
| `single_mode_15_advice_recommend` | `id, turn_num` | 6 | 已有 | turn 3 / 12 / 24 / 36 / 48 / 60（待解释） |
| `single_mode_15_check_point_effect` | `id, schedule_id, result_state, effect_type, effect_value_1..4` | 20 | 已有 | 進捗発表会 / 本番效果 |
| `single_mode_15_finals_act_effect` | `id, condition_type, condition_value, effect_type, effect_value_1..4` | 4 | 已有 | 决赛演出效果，待解读 |
| `single_mode_15_fusion_effect` | `id, advice_id, condition_type, condition_value, effect_type, effect_value_1..4` | 9 | 已有 | 転成效果 |
| `single_mode_15_outing_effect` | `id, support_card_id, story_step, add_level` | 10 | 已有 | 卡 30323 / 10149，5 段各 +1 |
| `single_mode_15_schedule` | `id, turn_num, need_act_num, need_act_level` | 3 | 已有 | turn 24/48/68；need_act_num 5；Lv 3/5/6 |

- `single_mode_scenario` row 15 另给出：`max_speed 1000 / max_stamina 650 / max_pow 600 / max_guts 500 /
  max_wiz 600`，`upper_limit_* 2500`，`skill_upgrade_num 2`，`gimmick_discount_rate 5000`，
  `max_skill_point 99999`。语义待与引擎字段对齐。

## 5. 待确认问题

> 结论列来自 6 局实机抓包（URA 保存的 `single_mode_festa-*` 包 + master.mdb），证据与复现方式见
> [`evidence/capture_report.md`](evidence/capture_report.md)。「已确认」= 规则与游戏下发的预测 / 实际结算逐条一致；
> 「推断」= 数据支持但未逐项隔离；「未观测」= 样本中没有可判定的情形。

| # | 问题 | 当前理解 | 备选 | 影响面 | 结论 |
|---|---|---|---|---|---|
| 1 | gauge 友情 +2 的范围 | 仅彩人物 +2 | 有彩圈全部 +2 | 资源账本 / 训练值 | **已确认**：仅处于友情训练的演目担当 +2（卡种 = 本训练且羁绊 ≥80），其余担当各 +1，同一演目累加，上限为该演目剩余 gauge（`need − gauge`）；训练失败不加 gauge。[实机](evidence/capture_report.md#q1) |
| 2 | 休息 / 非目标比赛 +1 加哪条 | 最低的一条 | 全部 / 随机 | 资源账本 | **已确认**：休息 / 外出 +1 给「未满 gauge 的演目中 Lv 最低 → gauge 最低 → act_type 最小」的一条；比赛同样给该条 +1，部分回合另有额外 gauge（来源未明）。[实机](evidence/capture_report.md#q2) |
| 3 | 演目稽古是否吃卡 buff / 彩圈 | 是（训练同 ID） | 部分 / 否 | 训练值 | **推断：吃**。演目稽古（5101–5105）的 home 参数随在场人头变化，同普通训练；另叠加 festa 参数（演目效果），实得 = 两者之和。[实机](evidence/capture_report.md#q3) |
| 4 | 多演目同时亮起的交互 | 可延期、选一个不影响其他 | 亮起即消费 | 动作集 | **已确认**：可同时多条亮起；执行其一后其余保持满，不会过期。[实机](evidence/capture_report.md#q4) |
| 5 | 合宿延长量 | 按目标赛场次 / 固定 1 回合 | 链式累加 | 阶段、赛程 | **未观测**：三次合宿固定 turn 13–16 / 37–40 / 61–64；样本中合宿期内无目标赛。[实机](evidence/capture_report.md#q5) |
| 6 | 友人补位是否分身 | 单次补位 | 多位置分身 | 人头布局 | **未定**：同一张编成卡可在同回合出现在 2–3 个指令中（普通训练仅见 target 1；有演目稽古时 1–5 均有）；友人卡未见。原因未明。[实机](evidence/capture_report.md#q6) |
| 7 | 同名双人头 | 编成内 + 编成外可并存 | 去重 | 人头 / gauge | **已确认（单一样本）**：去重。编成卡与 `act_member` 同角色时不另生人头，由编成卡担任该演目。[实机](evidence/capture_report.md#q7) |
| 8 | 教え転成是否保留基础效果 | 保留（自身 + 転成） | 覆盖 | 教え数值 | **推断**：材料教え移出列表，新教え以 `fusion_advice_id` 记录材料（601 ← 101）；材料自身效果是否保留未实测。[实机](evidence/capture_report.md#q8) |
| 9 | 伝受 / 発表会精确回合 | turn 24 / 48 / 68 末尾 | 年末回合内位置 | 事件 | **已确认**：発表会在 turn 24 / 48 / 68 当回合行动之后；24 / 48 后接転成 → 伝受（响应推进至 25 / 49）；其余伝受在 turn 3 开始与 turn 12 / 36 / 60 末（进入合宿）；68 后接本番。[实机](evidence/capture_report.md#q9) |
| 10 | finals act 效果 | 4 行数据 | — | 终局评分 | **推断（仅大成功样本）**：本番后事件全属性 +7、体力 +30、SP +400（1 例为根 / 智 +15、体力 +23）。[实机](evidence/capture_report.md#q10) |
| 11 | 進捗発表会未达标惩罚 | 拿不到大成功 | 降档 / 无奖励 | 结算 | **未观测**：样本内 発表会 全部 `result_type = 2`，各演目 Lv 均达标。[实机](evidence/capture_report.md#q11) |
| 12 | 组卡 ≥5 种是否含友人 | 含友人（21101 + 友） | 不含 | 组卡门禁 | **未观测**：样本只有一套卡组。[实机](evidence/capture_report.md#q12) |

## 6. S0 核对结论

- 机制未确认项：gauge 归属 / 友情范围、合宿延长、友人补位分身、同名双人头、転成保留、finals act。
- 数值待确认项（已登记 `stubs.md`）：演目稽古三档、教え / 転成 / 発表会 / finals act 数值、
  训练基础值、组卡门禁口径。
- 用户确认日期：2026-10-26（建仓与代号）
- 结论：**未可进入 S2 全量实现**；可先做 S2 的数据 schema 与 master.mdb 表导入骨架，
  机制相关代码等 S0 收口。
