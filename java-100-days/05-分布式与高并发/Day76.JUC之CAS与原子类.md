# Day 76 · JUC 之 CAS 与原子类

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 理解 CAS（Compare And Swap）的思想与三个操作数
- 认识 `Unsafe` 类在 CAS 中的角色
- 掌握常用原子类及 CAS 的 ABA 问题

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：CAS 原理、Unsafe、自旋、ABA 问题、AtomicInteger / AtomicLong / LongAdder / AtomicReference
- 全篇重点，CAS 是 AQS 的底层基石

## 核心重点

- CAS 三要素：内存地址 V、预期旧值 A、新值 B
- 当且仅当 V == A 时，把 V 改成 B，否则不操作
- 自旋：失败就重试，直到成功
- `Unsafe`：直接操作内存的类，CAS 方法在它上面
- 原子类：`AtomicInteger`、`AtomicLong`、`AtomicBoolean`
- `LongAdder`：高并发分段计数，比 AtomicLong 吞吐高
- `AtomicReference`：原子更新引用
- `AtomicStampedReference`：用版本号解决 ABA
- ABA 问题：值从 A → B → A，CAS 以为没变过
- CAS 的缺点：自旋开销、只能保证一个变量原子、ABA

## 练习/任务

1. 用 `AtomicInteger` 重写 `i++` 多线程例子，结果正确
2. 在笔记里写：ABA 是什么，怎么解决
3. 列一张「synchronized vs Lock vs CAS」适用场景对比

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 跑通 AtomicInteger 多线程累加
- [ ] 能说清 CAS 三要素
- [ ] 写清 ABA 与 AtomicStampedReference
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 对比 synchronized / Lock / CAS
