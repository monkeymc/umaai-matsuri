# 交接提示词

> 对话上下文很长时，经用户同意后用一段话交接；新上下文只读本文件即可续接。
> 写法：只写「下一个窗口无法从代码 / git 状态里恢复」的信息，不写过程叙述。

- **当前目标**：`matsuri`（骏大祭 / scenarioId 15）剧本仓建仓与 S0 机制核对。
- **已完成**：S1 建仓——五 crate 改名、依赖路径、引擎静态数据、CI、文档骨架；
  master.mdb `single_mode_15_*` 11 张表结构已登记在 `docs/plan/intake.md`。
- **进行中**：S0——gauge 归属 / 友情范围、合宿延长、友人补位分身、同名双人头、
  転成保留、finals act 效果待收口。
- **下一步**：
  1. 用真机帧 / 官方资料收口 `docs/plan/intake.md` §5 的待确认项；
  2. 结论落 `docs/mechanism.md` / `docs/glossary.md` / `docs/plan/stubs.md`；
  3. 进入 S2：把 master.mdb 表导入剧本资产 schema，替换 `sample_resource` /
     `training_basic_value` / `sample_order` 占位。
- **关键文件**：`docs/plan/intake.md`、`docs/mechanism.md`、`docs/glossary.md`、
  `docs/plan/scenario_dev_plan.md`、`crates/matsuri-core/src/game/`、`data/scenario_matsuri.json`。
- **常用命令**：`cargo fmt --all --check`、`cargo clippy --workspace --all-targets --offline -- -D warnings`、
  `cargo test --workspace --release --offline`、`cargo run --release --bin matsuri -- manual`。
- **约束 / 未决**：不照搬 mee-umasim 的早期算法；机制未确认不得用 stub 绕过；
  剧本代码不修改引擎仓；提交前统一更新 README / 文档。
