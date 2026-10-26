# 数值占位登记（STUB / TBD）

> 机制已确认、数值未到时的统一登记处。**占位不阻塞开发**，但必须可整体替换、可盘点。
> 规则：
> - 占位集中放在剧本资产 / 配置 / 常量表，代码里不散落魔法数；
> - 代码标记统一为 `STUB:`（临时占位）与 `TBD:`（待补逻辑/数值），并在此表登记；
> - 替换为确认值后：重录基线，并在 [`glossary.md`](../glossary.md) 的决定记录写明位移原因与影响面。

## 登记表

| 项 | 当前占位值 | 来源 | 存放位置 | 替换条件 | 基线影响 | 状态 |
|---|---|---|---|---|---|---|
| `sample_resource` | `0` | 模板占位 | `data/scenario_matsuri.json` / `MatsuriData` / `MatsuriView` | 5 条演目 gauge 账本定形（S2/S5） | 会位移 | OPEN |
| `training_basic_value` | 模板样例 2 档 × 5 训练 | 模板 | `data/scenario_matsuri.json` | 演目稽古 / 普通训练基础值从 master.mdb + 真机提取（S2） | 会位移 | OPEN |
| `sample_order` | `[0, 1, 2, 3, 4]` | 模板 | `data/matsuri_config.toml` / `MatsuriConfig` | 手写训练员偏好表定形（S7） | 会位移 | OPEN |
| master.mdb `single_mode_15_*` 导入 | 未导入，仅登记结构 | 静态数据 | `docs/plan/intake.md` §4 | S2 数据层按表 schema 导入 / 转换 | 会位移 | TBD |
| `scenario_id` | `15` | data `single_mode_scenario` | `data/scenario_matsuri.json` | 已确认 | 无 | DONE |

## 代码标记约定

```rust
// STUB: 占位值，见 docs/plan/stubs.md
const SAMPLE_RATE: i32 = 100;
```

- `STUB`：值已存在但未确认，**可以跑**；
- `TBD`：逻辑或数据缺失，**需要后续补齐**（不要用它绕过机制未确认）。
- 标记只用于数值 / 数据占位；**机制未确认不得用 stub 绕过**（应按 S0 闸门先确认机制）。

## 收尾检查

- [ ] 所有 `STUB` / `TBD` 都已在本表登记；
- [ ] 每项都有替换条件与影响面；
- [ ] 替换后已重录基线并在 `glossary.md` 留痕；
- [ ] 代码中搜索 `STUB:` / `TBD:` 与登记表一致。
