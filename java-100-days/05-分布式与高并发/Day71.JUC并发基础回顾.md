# Day 71 · JUC 并发基础回顾

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 把 JavaSE 阶段学过的 `synchronized`、`volatile`、线程基础捡回来
- 理解 Java 内存模型（JMM）的三个特性：可见性、有序性、原子性
- 搞懂 happens-before 的基本规则

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：**八股四大件之一，面试八股重灾区**
  - 源文案提示：**全篇重点，时间充足的话建议跟敲代码**
- 今日进度：并发编程基础、线程状态、synchronized 回顾、volatile、JMM、happens-before
- 全篇重点，今天是后面 8 天的地基，务必扎实

## 核心重点

- 线程 6 状态：NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING、TERMINATED
- `synchronized`：保证原子性 + 可见性 + 有序性，重量级锁
- `volatile`：保证可见性 + 禁止指令重排，**不保证原子性**
- JMM（Java Memory Model）：主内存 vs 工作内存
- 可见性：一个线程改了变量，另一个线程能立刻看到
- 有序性：编译器 / CPU 指令重排带来的问题
- 原子性：一组操作要么全做要么全不做
- happens-before：前一个操作的结果对后一个操作可见
- 指令重排与内存屏障的直觉

## 练习/任务

1. 写一个经典例子：两个线程同时 `i++` 一万次，观察结果不是 2 万
2. 给变量加上 `volatile`，再跑一次，理解它为什么解决不了 `i++` 的问题
3. 在笔记里写：volatile 和 synchronized 的区别

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合（JUC 学习期间保持手感）
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 跑通 `i++` 多线程丢更新例子
- [ ] 能说清 volatile 三个特性里不保证哪个
- [ ] 写清 volatile vs synchronized 区别
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 列出线程 6 状态
