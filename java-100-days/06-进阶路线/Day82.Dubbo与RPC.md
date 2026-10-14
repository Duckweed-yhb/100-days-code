# Day 82 · Dubbo 与 RPC

> 阶段：06-进阶路线 ｜ 重要程度：源文案未标注 ｜ 源视频：https://www.bilibili.com/video/BV1VE411q7dX/

## 今日目标

- 理解什么是 RPC，为什么微服务之间要走 RPC 而不是直接 HTTP 调接口
- 跟着 Dubbo 视频，认识一个 RPC 框架的完整组成
- 能说清一次远程调用从发出请求到拿到结果大致经过哪些环节

## 学习内容

- **源路线**：进阶路线之 Dubbo
  - 视频：https://www.bilibili.com/video/BV1VE411q7dX/
- 今日进度：RPC 概念与本地调用的区别、Dubbo 整体架构（Provider / Consumer / 注册中心）、一次远程调用的流程、序列化与网络传输名词
- 源文案提示：今天先建立整体认识，注册中心细节（Zookeeper）放到 Day90 再展开，今天不深挖

## 核心重点

- RPC（远程过程调用）要解决的问题：像调本地方法一样调远程服务
- 一次 RPC 的关键环节：序列化 → 网络传输 → 反序列化 → 执行 → 返回结果
- Dubbo 角色：Provider（服务提供方）、Consumer（消费方）、Registry（注册中心）
- 服务注册与发现：Provider 启动时把自己注册到注册中心，Consumer 从注册中心拿到 Provider 地址
- 负载均衡：Consumer 侧从多个 Provider 地址里挑一个调用
- 常见序列化方式（Hessian、JSON 等）只作名词了解
- 通信协议与 HTTP 的区别：长连接、二进制报文、更轻量
- Dubbo 与 Spring Boot 的整合：`@DubboService` / `@DubboReference` 注解的作用

## 练习/任务

1. 跟着视频搭一个最小的 Dubbo Demo：一个 Provider、一个 Consumer，Consumer 成功调用 Provider 的方法
2. 在笔记里画一张 Provider / Consumer / Registry 三者关系图，标注「注册」「订阅」「调用」三条线
3. 用自己的话写一段 200 字以内的解释：「什么是 RPC，它和直接发 HTTP 请求有什么不同」

## 今日刷题

- hot100 第 2 遍收尾 / 第 3 遍起步，今天 2 道
- 穿插 LeetCode 75 题单分类补充：今天可挑「链表」或「二叉树遍历」分类里还不熟的题
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目量不需要很多，但要反复刷

## 打卡清单

- [ ] 看完 Dubbo 视频中 RPC 概念与架构对应章节
- [ ] 跑通最小 Dubbo 调用 Demo
- [ ] 能画出 Provider / Consumer / Registry 关系图
- [ ] 今日刷题 2 道（hot100 二刷/三刷，或 LeetCode 75 分类补充）
- [ ] 用自己的话写清楚「什么是 RPC」
