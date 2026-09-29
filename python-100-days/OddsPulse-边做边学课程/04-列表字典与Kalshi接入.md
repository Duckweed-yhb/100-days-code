# 第 04 课：列表/字典进阶与 Kalshi 接入

## 学习目标
- 掌握列表推导、排序、过滤、循环处理
- 实现 Kalshi 数据源，与 Polymarket 共用 `MarketSource` 接口
- 双数据源都归一成 `Market` 模型

## 前置要求
- 完成课 03（有 `base.py` 的接口和 `polymarket.py`）

## 最小知识
- **列表推导**：`[m for m in markets if m.probability > 0.5]` 一次生成过滤后的列表。
- **排序**：`sorted(markets, key=lambda m: m.volume, reverse=True)` 按成交量降序。
- **接口复用**：Kalshi 只要"实现同样的方法"，下游不用改任何代码——这就是"依赖抽象不依赖实现"。
- Kalshi 的公开接口字段命名和 Polymarket 不同，你的工作就是**映射成同一套 `Market`**。

## 动手任务
1. 查看 Kalshi 公开 API 文档/示例，找到市场列表接口和字段（question、概率、成交量）。
2. 建 `odds_pulse/data/kalshi.py`，实现 `KalshiSource(MarketSource)`，字段归一成 `Market`。
3. 建一个简单的"数据源选择器"：`cli.py` 支持 `polymarket` 和 `kalshi` 两个子命令，都返回 `list[Market]`。
4. 练习列表推导和排序：写个临时脚本，把某数据源的市场按 volume 降序、过滤出 volume 前 10 的，打印 question。
5. 跑通双数据源，commit + push。

## 验收标准
- [ ] `python -m odds_pulse.cli kalshi` 打印出真实 Kalshi 市场（同样是 Market 结构）
- [ ] 代码里 Polymarket 和 Kalshi 的调用方式一致（都走 `MarketSource.fetch_markets()`）
- [ ] 能用列表推导/排序写出"volume 前 10"的过滤
- [ ] 已 push

## 常见坑
- Kalshi 接口可能需要分页（一次只返回一页）→ 先看返回里有没有 `next_cursor` / `count`，循环拉取。
- Kalshi 概率可能是整数百分比（如 62 表示 62%）→ 注意换算成 0-1 的 float。
- 别把两个数据源的字段名写死混在一起 → 全部在各自的 source 文件里完成映射。

## 预计时长
约 2.5 小时
