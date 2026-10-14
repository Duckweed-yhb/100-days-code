# Day 63 · RocketMQ 基础入门

> 阶段：05-分布式与高并发 ｜ 重要程度：4.9 ｜ 源视频：https://www.bilibili.com/video/BV1AU4y157LX/

## 今日目标

- 理解为什么要用消息队列，MQ 解决了什么问题
- 认识 RocketMQ 的整体架构和核心角色
- 跑通一个最简单的「生产者发消息 → 消费者收消息」Demo

## 学习内容

- **源路线**：RocketMQ 建议 1-2 天学完，重要程度 **4.9**
  - 视频：https://www.bilibili.com/video/BV1AU4y157LX/
  - 源文案：**除八股四大件之外的最重要的点**
  - 源文案提示：**全篇重点，时间充足的话建议跟敲代码**
- 今日进度：MQ 是什么、为什么用 MQ（异步、解耦、削峰）、RocketMQ 架构（NameServer / Broker / Producer / Consumer）、Topic 与队列、发送与消费消息
- 全篇重点，时间够就跟敲代码

## 核心重点

- MQ 的三大作用：异步处理、应用解耦、流量削峰
- 使用 MQ 的代价：系统复杂度、一致性、可用性降低
- RocketMQ 架构角色：NameServer（路由注册）、Broker（存储消息）、Producer（生产者）、Consumer（消费者）
- Topic：一类消息的主题
- Message Queue：Topic 下的队列，用于横向扩容
- 消息发送：同步发送、异步发送、单向发送
- 消息消费：推（Push）与拉（Pull）两种模式
- 消费组（Consumer Group）与集群消费 / 广播消费

## 练习/任务

1. 本地启动 NameServer 与 Broker，用官方 Demo 发一条消息、收一条消息
2. 在笔记里画出 RocketMQ 架构图，标出四个角色
3. 列一个「业务场景 → 该不该用 MQ」的小清单（如下单后发短信）

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：栈 / 队列 / 二叉树
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 跑通 Producer → Broker → Consumer 最小 Demo
- [ ] 能口述 MQ 的三大作用与代价
- [ ] 画完 RocketMQ 架构图
- [ ] 今日刷题 2 道（hot100 栈/队列/二叉树）
- [ ] 记录 NameServer 与 Zookeeper 的直觉区别
