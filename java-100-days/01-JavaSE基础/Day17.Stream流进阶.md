# Day 17 · Stream 流进阶

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 掌握 `collect` 收集成集合与 `groupingBy` 分组
- 会用 `max/min`、`reduce` 做聚合
- 了解并行流与 Stream 的注意事项

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 「Stream 流」进阶收尾
- 今日进度：collect 收集、拼接 joining、分组 groupingBy、聚合统计、并行流
- 源文案提示：**代码稍微写点就行**，熟悉常用 API 即可

## 核心重点

- `collect(Collectors.toList())` 收集回 List
- `Collectors.toSet()`、`Collectors.toMap()` 收集成 Set/Map
- `Collectors.groupingBy()` 按字段分组
- `Collectors.joining()` 拼接字符串（了解）
- `max(Comparator)`、`min(Comparator)` 求最大最小
- `reduce()` 聚合归约
- 并行流 `parallelStream()`（了解，线程安全问题后续再深入）
- 注意：一个 Stream 只能消费一次，终结后不能再用

## 练习/任务

1. 把过滤后的结果用 `collect(Collectors.toList())` 收集回新 List
2. 对一个对象列表用 `groupingBy` 按某个字段分组
3. 用 `max` 找出列表中最长的字符串
4. 尝试 `parallelStream()` 跑同一段代码，对比结果

## 打卡清单

- [ ] 会用 collect 收集成集合
- [ ] 会用 groupingBy 分组
- [ ] 会用 max/min 聚合
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
