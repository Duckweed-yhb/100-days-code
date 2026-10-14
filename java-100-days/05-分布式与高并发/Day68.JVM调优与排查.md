# Day 68 · JVM 调优与排查

> 阶段：05-分布式与高并发 ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV1yE411Z7AP/

## 今日目标

- 认识常用 JVM 参数（堆大小、新生代、元空间）
- 会用基础工具（jps / jstat / jmap / jstack）看运行时状态
- 理解常见 OOM 类型与排查思路

## 学习内容

- **源路线**：JVM 建议 5-6 天学完，重要程度 **5.0**
  - 视频：https://www.bilibili.com/video/BV1yE411Z7AP/
  - 源文案：八股四大件之一，面试八股重灾区
  - 源文案提示：全篇重点，时间充足的话建议跟敲代码
- 今日进度：常用 JVM 参数、GC 日志、命令行工具（jps / jstat / jmap / jstack）、可视化工具（JConsole / VisualVM）、OOM 与内存泄漏排查
- 全篇重点，工具不会用没关系，知道有这些工具、什么时候用即可

## 核心重点

- `-Xms`：堆初始大小；`-Xmx`：堆最大大小
- `-Xmn`：新生代大小；`-XX:MetaspaceSize`：元空间大小
- `-XX:+PrintGCDetails`：打印 GC 详情日志
- `jps`：列出正在运行的 Java 进程
- `jstat -gcutil`：查看 GC 统计
- `jmap`：导出堆快照（heap dump）
- `jstack`：查看线程栈，排查死锁、CPU 飙高
- 常见 OOM：Java heap space、GC overhead limit exceeded、Metaspace 溢出、unable to create new native thread
- 内存泄漏 vs 内存溢出：泄漏是原因，溢出是结果
- 排查思路：先看日志 → dump 堆 → MAT / JProfiler 分析大对象

## 练习/任务

1. 找一个自己写的 SpringBoot 工程，用 `-Xms256m -Xmx256m` 启动并打印 GC 日志
2. 用 `jps` 找到进程号，再用 `jstack` 把线程栈打出来看一眼
3. 在笔记里写一份「线上 OOM 排查 SOP」（5 步以内）

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：二叉树 / 回溯
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 会用 jps / jstat / jmap / jstack 基本命令
- [ ] 能列出至少 3 个常用 JVM 参数
- [ ] 写完 OOM 排查 SOP
- [ ] 今日刷题 2 道（hot100 二叉树/回溯）
- [ ] 区分内存泄漏与内存溢出
