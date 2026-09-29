# 第 10 课：README 撰写与发布（M3 交付）

## 学习目标
- 写出符合作业要求的完整 README（数据结构、框架设计、样例输出）
- 确认仓库干净可提交，正式发布 GitHub

## 前置要求
- 完成课 09（全流程 + 样例输出都在）

## 最小知识
作业要求 README 必须清楚写出四样：**数据结构、框架设计、样例输出**。参考 TradingAgents 的 README 风格（架构图 + 模块说明 + 复现方式），但不抄。

一个能过筛的 README 结构：
1. **一句话简介**：OddsPulse 是什么（预测市场数据源 + 分析框架）
2. **架构图**：数据接入 → 存储 → 分析 → Agent 判断（ASCII 或 mermaid）
3. **数据结构**：`Market` 字段表、`market_snapshots` 表结构
4. **框架设计**：每个模块干什么、打分公式、异常阈值怎么定的（讲清取舍）
5. **快速开始**：clone → venv → pip install → 一条命令跑出样例
6. **样例输出**：引用 `examples/sample_output.json` 并贴一段
7. **技术栈与依赖**、**未来方向**

## 动手任务
1. 重写 `README.md`（覆盖默认的"# OddsPulse"），按上面 7 段组织。
2. 架构图用 ASCII 或 mermaid 画清楚。
3. `git status` 检查：确认 `.env`、`data/*.db`、`.venv` 都不在待提交列表。
4. 写 `.gitignore` 完善（`.venv/`、`data/`、`.env`、`__pycache__/`、`*.db`）。
5. commit + push。在 GitHub 页面确认 README 渲染正常、README 里的命令别人能跑。
6.（可选）加 LICENSE（MIT）。

## 验收标准
- [ ] README 四要素齐全：数据结构、框架设计、样例输出、快速开始
- [ ] 仓库无 `.env` / `.db` / `.venv` 泄漏
- [ ] GitHub 页面 README 正常渲染，命令可复现
- [ ] M3 完成，可以正式提交给 ARTi

## 常见坑
- README 空泛、没有真实样例 → 一定要贴真实跑出来的样例输出。
- 架构图画得花哨但和代码不符 → 图和代码必须对得上，面试会被追问。
- 提交前不检查，把 db/key 推上去 → 提交前必看 `git status`。

## 预计时长
约 2 小时
