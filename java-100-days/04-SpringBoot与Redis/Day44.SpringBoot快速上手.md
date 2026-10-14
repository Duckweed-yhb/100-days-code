# Day 44 · SpringBoot 快速上手

> 阶段：04-SpringBoot与Redis ｜ 重要程度：4.2 ｜ 源视频：https://www.bilibili.com/video/BV15b4y1a7yG/

## 今日目标

- 理解 SpringBoot 在后端开发中的定位：现在最主流的框架
- 用官方脚手架快速创建一个 SpringBoot 工程，跑起第一个 Web 接口
- 建立「学如何用 SpringBoot 做配置」的学习心态，不纠结底层原理

## 学习内容

- **源路线**：SpringBoot 建议 4-5 天学完，重要程度 **4.2**
  - 视频：https://www.bilibili.com/video/BV15b4y1a7yG/
  - 源文案定位：现在最主流的框架，但面试问的相对较少，但一定得会
- 今日进度：SpringBoot 简介、快速创建工程（Spring Initializr / IDEA 新建）、启动类与内嵌服务器、写第一个 Controller 接口并访问
- 源文案提示：**没啥重点非重点之分，基本上就是学如何用 SpringBoot 框架做配置，结合自己情况决定是否跟敲代码**

## 核心重点

- SpringBoot 是什么：自动配置、起步依赖、内嵌服务器，简化 Spring 开发
- 如何创建工程：Spring Initializr / IDEA 选 Spring Initializr
- 起步依赖（starter）的概念，一个依赖引入一整套相关库
- 启动类：`@SpringBootApplication` 与 main 方法
- 内嵌 Tomcat，启动 main 方法即可跑起 Web 服务
- 第一个接口：`@RestController` + `@RequestMapping`
- 端口与首页访问验证（浏览器 / 接口工具调通）

## 练习/任务

1. 用 Spring Initializr（或 IDEA）新建一个 SpringBoot 工程，勾选 Web 起步依赖
2. 编写一个最简单的 Controller，启动后在浏览器访问到返回结果
3. 观察启动日志里内嵌 Tomcat 的端口信息
4. 跟不跟敲代码结合自己情况决定：先跑通，再决定是否逐行跟写

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天做 **1-2 道简单题**，重点是**回顾之前已经刷过的分类**，保持手感
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目不在多，要反复刷

## 打卡清单

- [ ] SpringBoot 工程创建成功并能启动
- [ ] 第一个 Web 接口在浏览器 / 接口工具调通
- [ ] 会说出 SpringBoot「自动配置、起步依赖、内嵌服务器」三点好处
- [ ] 今日 hot100 简单题 1-2 道（回顾已刷分类）
- [ ] 记录今日遇到的启动 / 访问报错与解决办法
