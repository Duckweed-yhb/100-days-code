# Day 60 · SpringCloud 远程调用与网关

> 阶段：05-分布式与高并发 ｜ 重要程度：4.4 ｜ 源视频：https://www.bilibili.com/video/BV1LQ4y127n4/

## 今日目标

- 理解服务之间是怎么互相调用的（OpenFeign / RestTemplate）
- 认识负载均衡在消费端是怎么生效的
- 搞懂网关（Gateway）在微服务里的位置和作用

## 学习内容

- **源路线**：SpringCloud 建议 3-4 天学完，重要程度 **4.4**
  - 视频：https://www.bilibili.com/video/BV1LQ4y127n4/
  - 源文案：现在非常流行的微服务，但面试问的相对较少
  - 源文案提示：代码根据自己情况决定跟不跟敲，不是很有必要跟
- 今日进度：远程调用（RestTemplate → OpenFeign）、负载均衡（Ribbon / LoadBalancer）、服务网关（Gateway）路由与断言、过滤器
- 重点是搞清楚「请求是怎么从一个服务打到另一个服务的」，配置类的东西会查即可

## 核心重点

- RestTemplate 手动发 HTTP 请求的写法
- OpenFeign：声明式远程调用，写接口 + 注解即可
- OpenFeign 与 Nacos 配合：服务名代替 IP
- 负载均衡：消费端从注册中心拿到服务列表后怎么选一台
- Ribbon 与 Spring Cloud LoadBalancer 的关系
- 网关 Gateway 的位置：所有外部请求统一入口
- 路由（Route）、断言（Predicate）、过滤器（Filter）三要素
- 网关做统一鉴权、跨域、日志的直觉

## 练习/任务

1. 在 consumer 里用 OpenFeign 写一个调用 provider 接口的声明式客户端
2. 把 provider 起两个端口，观察负载均衡是怎么轮询的
3. 画一张「请求 → 网关 → consumer → provider」的链路图

## 今日刷题

- hot100 第 2 遍，今天按分类刷 **2 道**，分类：数组 / 字符串 → 哈希 / 链表
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解
- 反复刷：刷到能够看到题就默写的程度，刷完一遍刷第二遍

## 打卡清单

- [ ] 能用 OpenFeign 写出一个远程调用接口
- [ ] 能解释网关在整个链路里的位置
- [ ] 画完请求链路图
- [ ] 今日刷题 2 道（hot100 哈希/链表）
- [ ] 记录 OpenFeign 与 RestTemplate 的区别
