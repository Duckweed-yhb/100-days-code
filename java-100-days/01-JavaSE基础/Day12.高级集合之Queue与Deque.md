# Day 12 · 高级集合之 Queue 与 Deque

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 掌握 `Queue` 单端队列与 `Deque` 双端队列的特点
- 会用 `ArrayDeque`、`LinkedList` 模拟队列与栈
- 了解 `PriorityQueue` 优先队列的基本使用

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 源文案重点「高级集合」今日攻克；也在为 Day20 的数据结构概览提前铺垫
- 今日进度：Queue 接口、Deque 双端队列、ArrayDeque、PriorityQueue
- 源文案提示：**代码稍微写点就行**，先认识这些实现类

## 核心重点

- `Queue` 单端队列：先进先出（FIFO），`offer()` 入队、`poll()` 出队、`peek()` 看队首
- `Deque` 双端队列：两端都可进出
- `ArrayDeque`：Deque 常用实现，数组结构，可当栈用
- `LinkedList` 也实现了 Deque
- 用 Deque 模拟栈：`push()` 入栈、`pop()` 出栈、`peek()` 看栈顶（后进先出 LIFO）
- `PriorityQueue` 优先队列：按优先级出队，不是先进先出
- 栈、队列在后续刷题中会频繁用到（与 Day20 数据结构对应）

## 练习/任务

1. 用 ArrayDeque 模拟一个队列，依次入队出队，观察 FIFO
2. 用 Deque 的 push/pop 模拟栈，观察 LIFO
3. 创建一个 PriorityQueue，放入几个数，观察出队顺序
4. 整理「队列 / 栈 / 优先队列 → Java 实现类」对照笔记

## 打卡清单

- [ ] 理解 Queue 与 Deque 区别
- [ ] 会用 ArrayDeque 模拟队列与栈
- [ ] 认识 PriorityQueue
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
