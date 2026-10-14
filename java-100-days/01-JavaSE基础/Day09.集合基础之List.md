# Day 09 · 集合基础之 List

> 阶段：01-JavaSE基础 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV17F411T7Ao/

## 今日目标

- 理解集合体系顶层 `Collection` 与数组的区别
- 掌握 `List` 接口特点与 `ArrayList`、`LinkedList`
- 会用集合增删改查与遍历

## 学习内容

- **源路线**：JavaSE 建议 20-25 天学完，重要程度 **5.0**
  - 本阶段视频（见 Day01）：https://www.bilibili.com/video/BV17F411T7Ao/、https://www.bilibili.com/video/BV1yW4y1Y7Ms/
  - 源文案重点清单「集合」今日开始
- 今日进度：集合框架概述、Collection 接口、List 接口、ArrayList、LinkedList
- 源文案提示：**代码稍微写点就行**，熟悉集合基本操作

## 核心重点

- 集合与数组区别：长度可变、只能存引用类型、提供丰富方法
- `Collection` 顶层接口常用方法：`add()`、`remove()`、`contains()`、`size()`、`clear()`
- `List` 特点：有序、可重复、有索引
- `ArrayList`：底层数组，查询快、增删慢
- `LinkedList`：底层链表，查询慢、增删快，特有 `addFirst/Last`、`removeFirst/Last`
- List 特有方法：带索引的 `add(index,e)`、`remove(index)`、`get(index)`、`set(index,e)`
- 三种遍历：迭代器 Iterator、增强 for、普通 for 索引

## 练习/任务

1. 创建一个 `ArrayList<String>`，添加、删除、修改元素
2. 分别用迭代器、增强 for、普通 for 遍历同一个 List
3. 用 LinkedList 模拟队列或栈的存取
4. 写一句注释：ArrayList 与 LinkedList 分别适合什么场景

## 打卡清单

- [ ] 会用 Collection 常用方法
- [ ] 掌握 ArrayList 增删改查
- [ ] 能用三种方式遍历 List
- [ ] 今天投入学习约 1.5 小时
- [ ] 记录今天遇到的报错与解决办法
