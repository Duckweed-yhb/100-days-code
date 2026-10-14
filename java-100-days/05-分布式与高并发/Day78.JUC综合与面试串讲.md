# Day 78 · JUC 综合与面试串讲

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 把 JUC 这 8 天的内容串成面试可答的回答
- 覆盖 synchronized vs Lock、AQS、ConcurrentHashMap、线程池、CAS、volatile 等高频八股
- 形成自己的 JUC 知识地图

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：不看新章节，专注把 Day71-77 的笔记串成思维导图
- 重点：把「锁 → AQS → 容器 → 线程池 → CAS → 工具类」串成一条线
- 时间富余的话，把之前没跟敲的代码补一下

## 核心重点

- synchronized 与 Lock 的区别
- volatile 与 synchronized 的区别
- AQS 原理：state + CLH 队列
- ReentrantLock 公平 vs 非公平
- ConcurrentHashMap 1.7 vs 1.8
- ThreadPoolExecutor 7 参数与执行流程
- 4 种拒绝策略
- CAS 原理与 ABA
- CountDownLatch vs CyclicBarrier vs Semaphore
- JMM：可见性、有序性、原子性

## 练习/任务

1. 合上讲义，凭记忆写一份 JUC 知识大纲，再对照笔记补漏
2. 录音回答 3 道高频题：「讲讲 AQS」「讲讲线程池」「ConcurrentHashMap 原理」
3. 把卡壳的题整理成「JUC 待巩固清单」

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 凭记忆写出 JUC 知识大纲
- [ ] 录音回答 3 道高频题
- [ ] 整理完「JUC 待巩固清单」
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 能脱稿讲完 AQS 主线
