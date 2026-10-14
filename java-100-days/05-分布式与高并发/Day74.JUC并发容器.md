# Day 74 · JUC 并发容器

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 搞清楚为什么 `HashMap` / `ArrayList` 不能在多线程下用
- 掌握 `ConcurrentHashMap` 1.7 与 1.8 的区别
- 认识常见阻塞队列及适用场景

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：并发容器概览、ConcurrentHashMap、CopyOnWriteArrayList、BlockingQueue 家族、ConcurrentLinkedQueue
- 全篇重点，`ConcurrentHashMap` 是面试必问

## 核心重点

- `Hashtable`：全表 synchronized，效率低
- `Collections.synchronizedXxx`：包装类，全局锁
- `ConcurrentHashMap`：分段锁 / CAS + synchronized 桶锁
- JDK7 ConcurrentHashMap：Segment 分段锁
- JDK8 ConcurrentHashMap：去掉 Segment，数组 + 链表/红黑树 + CAS + synchronized
- `CopyOnWriteArrayList`：写时复制，读多写少
- `ArrayBlockingQueue`：有界数组阻塞队列
- `LinkedBlockingQueue`：链表阻塞队列（线程池默认用它）
- `PriorityBlockingQueue`、`DelayQueue`：优先级 / 延时
- 阻塞队列的 `put` / `take`：拿不到就阻塞

## 练习/任务

1. 写个多线程同时 `HashMap.put` 的小 Demo，观察丢数据 / 死循环
2. 换成 `ConcurrentHashMap` 再跑一遍
3. 在笔记里写：ConcurrentHashMap 1.7 vs 1.8 的区别

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 观察到 HashMap 多线程下的问题
- [ ] 写清 ConcurrentHashMap 1.7 vs 1.8
- [ ] 能说出至少 3 种 BlockingQueue
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 理解 CopyOnWriteArrayList 为什么读不加锁
