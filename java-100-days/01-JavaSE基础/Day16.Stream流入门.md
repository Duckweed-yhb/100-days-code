# Day 16 · Stream 流入门

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 理解 Stream 流的思想（声明式处理集合）
- 掌握 Stream 的获取、中间操作与终结操作三步走
- 会用 filter、map、forEach 做基本集合处理

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 源文案重点清单「Stream 流」今日开始
- 今日进度：Stream 概述、获取流、中间方法、终结方法
- 源文案提示：**代码稍微写点就行**，会链式调用即可

## 核心重点

- Stream 不是数据结构，是用来处理集合/数组的「流水线」
- 获取流：`集合.stream()`、`Arrays.stream(数组)`、`Stream.of()`
- 三步走：创建流 → 中间操作（可多条）→ 终结操作（触发执行）
- 常见中间方法：`filter()` 过滤、`map()` 转换、`limit()`、`skip()`、`distinct()`
- 常见终结方法：`forEach()` 遍历、`count()` 计数
- 中间操作返回新流，可以链式调用；终结后流就关了
- Stream 不修改原数据源

## 练习/任务

1. 准备一个 `List<String>`，用 `stream().filter().forEach()` 过滤并打印
2. 用 `map()` 把字符串列表全部转成大写
3. 用 `limit()` 和 `skip()` 实现分页效果
4. 写一句注释：Stream 三步走分别是什么

## 打卡清单

- [ ] 理解 Stream 三步式用法
- [ ] 会用 filter / map / forEach
- [ ] 知道中间操作与终结操作区别
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
