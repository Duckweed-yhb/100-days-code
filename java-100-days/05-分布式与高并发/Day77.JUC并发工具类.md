# Day 77 · JUC 并发工具类

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV16J411h7Rd/

## 今日目标

- 掌握 CountDownLatch、CyclicBarrier、Semaphore 的用法与区别
- 理解它们分别解决什么并发协作问题
- 知道什么时候该用哪个

## 学习内容

- **源路线**：JUC 建议 8-9 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV16J411h7Rd/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：CountDownLatch、CyclicBarrier、Semaphore、Exchanger 简介
- 全篇重点，这几个工具类在项目里很常用

## 核心重点

- `CountDownLatch`：倒数门闩，等待 N 个事件完成后再继续（一次性）
- `CountDownLatch`：`countDown()` 减一，`await()` 阻塞等待归零
- `CyclicBarrier`：循环栅栏，N 个线程互相等待到齐后再一起冲破
- CyclicBarrier 可复用（reset），CountDownLatch 不可复用
- `Semaphore`：信号量，控制同时访问的线程数（如限流）
- `Semaphore.acquire()` / `release()`
- `Exchanger`：两个线程之间交换数据
- 典型场景：启动时等所有子线程就绪（CountDownLatch）
- 典型场景：多线程分治后在某个点集合（CyclicBarrier）
- 典型场景：接口限流、同时只允许 N 个请求（Semaphore）

## 练习/任务

1. 用 CountDownLatch 写「主线程等 5 个子线程都跑完再汇总」
2. 用 CyclicBarrier 写「5 个线程都准备好再同时出发」
3. 在笔记里写：CountDownLatch vs CyclicBarrier 的区别

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：动态规划 / 贪心 / 综合
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 跑通 CountDownLatch Demo
- [ ] 跑通 CyclicBarrier Demo
- [ ] 写清两者区别
- [ ] 今日刷题 2 道（hot100 DP/贪心/综合）
- [ ] 能用 Semaphore 写一个简单限流
