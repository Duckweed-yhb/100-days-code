# Day 72 · JUC 锁机制

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 掌握 `Lock` 接口与 `ReentrantLock` 的基本使用
- 理解公平锁 vs 非公平锁、可重入锁、读写锁
- 对比 `synchronized` 与 `ReentrantLock` 的区别

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：Lock 接口、ReentrantLock、公平锁 / 非公平锁、可重入、tryLock、Interruptibly、ReentrantReadWriteLock
- 全篇重点，面试高频

## 核心重点

- `Lock` 接口：`lock()` / `unlock()` / `tryLock()` / `lockInterruptibly()`
- `ReentrantLock`：可重入，手动释放锁（必须在 finally 里 unlock）
- 公平锁：先到先得；非公平锁：允许插队，默认非公平
- 可重入：同一个线程可以多次获得同一把锁
- `tryLock`：拿不到锁立刻返回 false，不死等
- `lockInterruptibly`：可响应中断的加锁
- `ReentrantReadWriteLock`：读共享、写独占，适合读多写少
- `synchronized` vs `ReentrantLock`：自动释放 vs 手动释放、可中断 vs 不可中断、可公平 vs 不可公平

## 练习/任务

1. 用 `ReentrantLock` 重写昨天的 `i++` 例子
2. 写一个 `ReentrantReadWriteLock` 的缓存例子：读并发、写互斥
3. 在笔记里写一张 synchronized vs ReentrantLock 对比表

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 会用 ReentrantLock（含 finally unlock）
- [ ] 能解释公平锁 vs 非公平锁
- [ ] 写完 synchronized vs ReentrantLock 对比表
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 理解读写锁为什么读可以共享
