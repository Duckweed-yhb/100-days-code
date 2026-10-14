# Day 43 · SSM 整合实战

> 阶段：03-Web与框架入门 ｜ 重要程度：4.0 ｜ 源视频：https://www.bilibili.com/video/BV1sC411L76f

## 今日目标

- 把 Spring、SpringMVC、MyBatis 三者整合成一个完整工程
- 跑通一次「浏览器请求 → SpringMVC → Service → MyBatis → MySQL → 返回页面」的完整链路
- 收尾本阶段，形成对 Web 后端分层架构的整体认识

## 学习内容

- **源路线**：SSM（Spring、SpringMVC、Mybatis）时间看个人情况，重要程度 **4.0**，三大框架，但面试不咋问，现在也不会单独用，都用 SpringBoot 整合这些东西了，但属于底层基础，SpringBoot 只是整合，其底层还是这三大框架
  - 动力节点路线：https://www.bilibili.com/video/BV1sC411L76f
  - 源文案重点：**全篇重点，是否跟着敲代码看自己的掌握程度**
- 今日进度：三层架构（Controller / Service / DAO）回顾、Spring 与 MyBatis 整合、Spring 与 SpringMVC 整合、事务管理初步、一个最小 SSM 工程跑通
- 源文案提示：**全篇重点**，是否跟着敲代码看自己的掌握程度；现在企业都用 SpringBoot，但 SpringBoot 底层还是这三大框架

## 核心重点

- Web 后端三层架构：表现层（SpringMVC）、业务层（Service）、持久层（MyBatis）
- Spring 容器管理 Service 与 DAO，SpringMVC 容器只管理 Controller
- 整合思路：让 MyBatis 的 SqlSessionFactory 交给 Spring 管理
- 数据源与事务管理器交给 Spring 配置
- `@Transactional` 声明式事务（初步认识）
- 一个请求的完整链路：浏览器 → DispatcherServlet → Controller → Service → Mapper → DB → 原路返回
- 为什么现在企业用 SpringBoot：自动装配，免去大量 XML / 配置

## 练习/任务

1. 跟着视频搭一个最小 SSM 工程，启动 Tomcat 后浏览器能访问到一个查询接口
2. 完成一条完整业务：列表查询（前端传参 → Controller → Service → Mapper → MySQL → 页面渲染）
3. 在 Service 方法上加 `@Transactional`，故意制造一次异常，观察回滚效果
4. 画一张 SSM 三层架构图，把今天整条链路画进笔记
5. 是否跟敲：**全篇重点，是否跟着敲代码看自己的掌握程度**；掌握得好可以只看架构，掌握得弱就跟敲完整工程

## 今日刷题

- 本周复盘：把这一周刷过的 hot100 题目（数组、字符串、哈希、链表、栈、队列、二叉树、回溯、动态规划、贪心）挑 **2 道简单题** 重新做一遍
- 重点看：之前没做出来、靠看题解过的题，这次能不能独立写出来
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] SSM 整合工程跑通，浏览器能访问到查询接口
- [ ] 跑通一条完整的「Controller → Service → Mapper → DB」链路
- [ ] 画好 SSM 三层架构图
- [ ] 今日复盘刷题 2 道（本周做过的简单题重做）
- [ ] 能用自己的话讲清 SpringBoot 与 SSM 的关系
- [ ] 本阶段（Day32–Day43）学习笔记整理完毕
