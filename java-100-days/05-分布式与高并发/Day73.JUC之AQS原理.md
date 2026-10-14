# Day 73 · JUC 之 AQS 原理

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 知道 AQS 是什么，为什么它是 JUC 的基石
- 理解 state 状态变量 + CLH 等待队列的基本模型
- 能说清 ReentrantLock 是怎么基于 AQS 实现的

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：AQS 概念、state 同步状态、CLH 等待队列、acquire / release 模板方法、独占模式与共享模式
- 全篇重点，面试会问「讲讲 AQS」

## 核心重点

- AQS（AbstractQueuedSynchronizer）：JUC 锁的底层框架
- `state`：volatile int，表示同步状态（如重入次数）
- CLH 队列：双向链表，存放获取锁失败的线程
- 模板方法模式：AQS 定义骨架，子类（NonfairSync / FairSync）实现 tryAcquire / tryRelease
- 独占模式：同一时刻只一个线程能拿到（ReentrantLock）
- 共享模式：多个线程可同时拿到（Semaphore / CountDownLatch / ReentrantReadWriteLock 的读锁）
- `acquireSharedInterruptibly` 与 `acquireInterruptibly`
- 入队后 park 挂起，前驱释放后 unpark 唤醒
- ReentrantLock、ReentrantReadWriteLock、Semaphore、CountDownLatch 都基于 AQS

## 练习/任务

1. 在 IDEA 里打开 `AbstractQueuedSynchronizer` 源码，看一眼 `acquire` 方法的结构
2. 画一张「state + CLH 队列」草图
3. 列一个清单：JUC 里哪些工具类是基于 AQS 的

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 能说清 AQS 的两个核心：state + CLH 队列
- [ ] 知道 ReentrantLock 基于 AQS
- [ ] 列完 JUC 中基于 AQS 的工具类清单
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 区分独占模式与共享模式
