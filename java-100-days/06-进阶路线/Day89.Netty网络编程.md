# Day 89 · Netty 网络编程

> 阶段：06-进阶路线 ｜ 重要程度：源文案未标注 ｜ 源视频：https://www.bilibili.com/video/BV1py4y1E7oA/

## 今日目标

- 进入 Netty 学习，理解它为什么能替代原生 NIO 写出高性能网络程序
- 把 Day84 学过的 IO 多路复用（epoll）和 Netty 串起来
- 跑通一个最简单的 Netty 服务端 + 客户端 Demo

## 学习内容

- **源路线**：进阶路线之 Netty
  - 视频：https://www.bilibili.com/video/BV1py4y1E7oA/
- 今日进度：BIO / NIO / AIO 的区别、Netty 核心组件（EventLoop、Channel、Pipeline、ChannelHandler）、编解码器、心跳与断线重连名词
- 源文案提示：Netty 在面试中不是最高频，但 Dubbo、RocketMQ 底层都用到它，今天先把「是什么、为什么用、怎么跑通」三件事解决

## 核心重点

- 为什么需要 Netty：原生 NIO 编程复杂、容易踩坑、维护成本高
- BIO、NIO、AIO 的区别（呼应 Day84 的 IO 模型）
- Channel：网络连接抽象
- EventLoop：处理 IO 事件的线程模型
- ChannelPipeline 与 ChannelHandler：责任链模式处理入站 / 出站事件
- 编解码器：把字节流转成业务对象、把业务对象转成字节流
- 粘包 / 拆包问题的产生与常见解决思路（固定长度、分隔符、长度字段）
- 心跳机制：检测连接是否还活着
- 零拷贝在 Netty 中的体现（CompositeByteBuf 等名词）

## 练习/任务

1. 跟着视频写一个最简单的 Netty Hello World：服务端监听端口，客户端发一条消息，服务端打印并回写
2. 在笔记里画一张 Netty 请求处理流程图：Channel → Pipeline → 一串 ChannelHandler
3. 列出 Netty 里用到的设计模式（引导类建造者、Pipeline 责任链等），和 Day87/88 对照

## 今日刷题

- hot100 第 2 遍收尾 / 第 3 遍起步，今天 2 道
- 穿插 LeetCode 75 题单分类补充：今天可挑「图」或「贪心」分类里还不熟的题
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目量不需要很多，但要反复刷

## 打卡清单

- [ ] 看完 Netty 视频核心章节
- [ ] 跑通 Netty 服务端 + 客户端 Demo
- [ ] 能说清 Channel / EventLoop / Pipeline 各自是干嘛的
- [ ] 今日刷题 2 道（hot100 二刷/三刷，或 LeetCode 75 分类补充）
- [ ] 记录 Netty 里用到的设计模式
