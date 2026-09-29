# 第 09 课：整合 CLI 与样例输出

## 学习目标
- 把"拉数据 → 存储 → 分析 → agent判断"串成一条命令
- 生成一份样例输出文件（作业要求的"样例输出"）

## 前置要求
- 完成课 08（各模块都有）

## 最小知识
- **编排**：一个入口按顺序调用各模块，用 `argparse` 或简单 `if/elif` 处理子命令。
- **样例输出**：作业要"样例输出"，你要在仓库里放一份**真实跑出来的结果文件**（如 `examples/sample_output.json`），README 引用它。这比截图更有说服力，且是真实的。
- **可复现**：`README` 写清"克隆后 `pip install -r requirements.txt` 就能跑出同样结果"。

## 动手任务
1. 把 cli 整理成清晰结构：`polymarket` / `kalshi` / `extract` / `signals` / `verdict` / `run`（全流程）。
2. 加 `run` 子命令：拉双源 → 入库 → 提取 → 检测 → agent判断 → 汇总打印。
3. 生成 `examples/sample_output.json`：真实跑一次 `run`，把结果写成文件（含市场、概率、变化、信号、verdict）。
4. 写一个 `examples/` 下的说明（或并入 README）：解释样例文件每个字段。
5. commit + push。

## 验收标准
- [ ] `python -m odds_pulse.cli run` 一条命令跑完整个流程
- [ ] `examples/sample_output.json` 是真实输出，字段有注释/文档
- [ ] 能在 README 里写"怎么复现这个样例"
- [ ] 已 push

## 常见坑
- cli 变成一大坨 → 把每个子命令拆成小函数，各管一段。
- 样例输出写死/伪造 → 必须是真实跑出来的，否则面试一跑就穿帮。
- 忘了 .gitignore 保护 .env 和 .db → 提交前 `git status` 检查一遍。

## 预计时长
约 2 小时
