# 第 03 课：数据模型与 Polymarket 接入（M1 交付）

## 学习目标
- 用 `dataclass` / Pydantic 定义结构化数据模型
- 实现 Polymarket 数据接入模块，输出真实市场数据
- 完成 **M1 骨架**，把可运行的交付物 push 到 GitHub

## 前置要求
- 完成课 01-02（能拉 JSON、知道字典/列表）

## 最小知识
- **dataclass**：定义"有哪些字段"的数据结构。比字典更好读、更不易错。
  ```python
  from dataclasses import dataclass
  @dataclass
  class Market:
      id: str
      question: str      # 市场问题，如"特朗普会赢得2028大选吗"
      probability: float # 当前概率
      volume: float      # 成交量
  ```
- **Pydantic**（可选进阶）：能自动校验字段类型，README 提它更专业。
- **统一接口思想**：Polymarket 和 Kalshi 的字段名不同，我们要把它们**归一成自己的 `Market` 模型**，让下游（分析、agent）只认一种结构。这是"数据接入层"的核心价值。

## 动手任务
1. 装 `pydantic`（或就用标准库 `dataclass`，二选一，README 说清）。
2. 在 `odds_pulse/data/models.py` 定义 `Market` 模型（字段：id/question/probability/volume/事件时间等）。
3. 建 `odds_pulse/data/base.py`，定义一个 `MarketSource` 接口（方法：`fetch_markets() -> list[Market]`）。
4. 建 `odds_pulse/data/polymarket.py`，实现 `PolymarketSource(MarketSource)`：调用 API → 把原始 JSON 里的每条市场**映射**成 `Market`。
5. 建 `odds_pulse/data/__init__.py` 导出这些类。
6. 改 `cli.py`：`python -m odds_pulse.cli polymarket` 能拉一批市场并打印（如前 5 条：question / probability / volume）。
7. **跑通 → commit → push**：`git add -A` → `git commit -m "feat: Polymarket data source with unified Market model"` → `git push`

## 验收标准
- [ ] `python -m odds_pulse.cli polymarket` 打印出真实市场的 question/probability/volume
- [ ] 每个返回对象都是 `Market` 类型（能打印字段，不是裸字典）
- [ ] 仓库已推送这个 M1 交付

## 常见坑
- API 字段名和你要的对不上 → 先在课 02 的 probe 里打印原始 JSON，找到确切字段名再映射。
- 概率可能是字符串 `"0.62"` 不是数字 → 用 `float(...)` 转换，或 Pydantic 自动转。
- 有些市场可能没有 volume 字段 → 映射时给默认值（如 `volume=0.0`）。

## 预计时长
约 2.5 小时
