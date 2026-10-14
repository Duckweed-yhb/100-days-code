# Day 75 · JUC 线程池

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 掌握 `ThreadPoolExecutor` 的 7 个核心参数
- 理解任务提交后的执行流程与拒绝策略
- 知道为什么《阿里规范》不建议直接用 `Executors`

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：为什么用线程池、ThreadPoolExecutor 7 参数、工作队列、拒绝策略、Executors 工厂方法、线程池大小怎么设
- 全篇重点，面试必问「讲讲线程池」

## 核心重点

- 线程池好处：复用线程、控制并发数、管理任务
- 7 参数：corePoolSize、maximumPoolSize、keepAliveTime、unit、workQueue、threadFactory、handler
- 执行流程：核心线程 → 队列 → 最大线程 → 拒绝策略
- 4 种拒绝策略：AbortPolicy（抛异常，默认）、CallerRunsPolicy、DiscardPolicy、DiscardOldestPolicy
- `newFixedThreadPool`：固定线程数，队列无界（OOM 风险）
- `newCachedThreadPool`：线程数无界（OOM 风险）
- `newSingleThreadExecutor`：单线程
- `newScheduledThreadPool`：定时任务
- 为什么阿里规范禁止 `Executors`：队列 / 线程数无界导致 OOM
- 线程池大小经验：CPU 密集型 ≈ CPU 核数 + 1；IO 密集型可适当放大

## 练习/任务

1. 自己 new 一个 ThreadPoolExecutor，核心 2、最大 4、队列容量 2，提交 8 个任务，观察触发拒绝策略
2. 故意把队列设成无界，观察任务堆积
3. 在笔记里写：为什么不建议用 Executors.newFixedThreadPool

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 能脱稿背出 ThreadPoolExecutor 7 个参数
- [ ] 跑通拒绝策略 Demo
- [ ] 写清 4 种拒绝策略
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 说清为什么禁用 Executors
