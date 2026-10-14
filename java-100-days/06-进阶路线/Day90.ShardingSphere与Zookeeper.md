# Day 90 · ShardingSphere 与 Zookeeper

> 阶段：06-进阶路线 ｜ 重要程度：源文案未标注 ｜ 源视频：https://www.bilibili.com/video/BV1ta411g7Jf/

## 今日目标

- 学完 ShardingSphere，理解分库分表要解决什么问题
- 学完 Zookeeper，理解它在分布式系统里的角色（呼应 Day82 Dubbo 注册中心）
- 给「06-进阶路线」阶段收尾，做好进入下一阶段的心理准备

## 学习内容

- **源路线**：进阶路线之 ShardingSphere 与 Zookeeper
  - ShardingSphere 视频：https://www.bilibili.com/video/BV1ta411g7Jf/
  - Zookeeper 视频：https://www.bilibili.com/video/BV1M741137qY/
- 今日进度：分库分表的背景与痛点、ShardingSphere 分库分表中间件的定位、Zookeeper 数据模型（ZNode）、Watcher 机制、ZAB 协议名词
- 源文案总结：**至此，基本上Java技术就学完了，剩下的就是深入**

## 核心重点

- 为什么要分库分表：单表数据量过大、单库写入压力过高
- 垂直拆分与水平拆分的区别
- 分库分表带来的问题：分布式 ID、跨库分页、分布式事务
- ShardingSphere 的定位：在应用和数据库之间做路由，对业务代码尽量透明
- Zookeeper 是干什么的：分布式协调服务（注册中心、配置中心、分布式锁）
- ZNode 数据节点：临时节点、永久节点、顺序节点
- Watcher 机制：节点数据变化时主动通知客户端
- ZAB 协议：Zookeeper 保证集群数据一致的协议（只作名词了解）
- Zookeeper 在 Dubbo 里扮演注册中心的角色（呼应 Day82）

## 练习/任务

1. 跟着 ShardingSphere 视频跑一个最简单的分库分表 Demo（按某个字段路由到不同表）
2. 本地起一个 Zookeeper，用客户端工具连上，创建一个临时节点并观察会话断开后节点消失
3. 在笔记里用一段话总结：「ShardingSphere 解决什么问题」「Zookeeper 解决什么问题」

## 今日刷题

- hot100 第 2 遍收尾 / 第 3 遍起步，今天 2 道
- 穿插 LeetCode 75 题单分类补充：今天可挑一个之前一直跳过的分类（如「设计」「区间」）看看
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目量不需要很多，但要反复刷

## 打卡清单

- [ ] 看完 ShardingSphere 与 Zookeeper 对应视频章节
- [ ] 跑通一个最简分库分表示例
- [ ] 本地起过 Zookeeper 并创建过临时节点
- [ ] 今日刷题 2 道（hot100 二刷/三刷，或 LeetCode 75 分类补充）
- [ ] 写一句总结：进阶路线 11 天走完，下一步准备深入哪个方向
