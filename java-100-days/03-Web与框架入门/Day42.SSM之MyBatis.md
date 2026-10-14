# Day 42 · SSM 之 MyBatis

> 阶段：03-Web与框架入门 ｜ 重要程度：4.0 ｜ 源视频：https://www.bilibili.com/video/BV1Ft4y1g7Fb/

## 今日目标

- 理解 MyBatis 解决了 JDBC 的哪些痛点
- 掌握 MyBatis 核心配置：SqlSessionFactory、Mapper 接口与 XML 映射
- 完成一次「Java 方法 → 执行 SQL → 拿到结果对象」的完整流程

## 学习内容

- **源路线**：SSM（Spring、SpringMVC、Mybatis）时间看个人情况，重要程度 **4.0**
  - 动力节点路线：https://www.bilibili.com/video/BV1Ft4y1g7Fb/
  - 源文案重点：**全篇重点，是否跟着敲代码看自己的掌握程度**
- 今日进度：MyBatis 简介、核心配置文件、Mapper 接口与 XML、增删改查、参数映射、结果集映射
- 源文案提示：**全篇重点**，是否跟着敲代码看自己的掌握程度

## 核心重点

- MyBatis 是什么：一个持久层框架，把 SQL 与 Java 代码解耦
- 对比原生 JDBC：不用手动注册驱动、手动关连接、手动封装结果集
- 核心配置文件：数据源、Mapper 映射文件位置
- `SqlSessionFactory` 与 `SqlSession` 的概念
- Mapper 接口 + XML 映射文件：接口方法与 SQL 语句一一对应
- `select` / `insert` / `update` / `delete` 标签
- `#{}` 占位符：预编译参数，防止 SQL 注入
- 实体类属性与数据库字段的结果映射（`resultMap`）
- 模糊查询、多参数传递的初步写法

## 练习/任务

1. 新建一个 MyBatis 项目，连之前用过的 MySQL 库，跑通第一个 `select` 查询
2. 写一个用户表的完整增删改查：Mapper 接口 + XML 配置 + 测试方法
3. 故意把 XML 里的 SQL 写错，观察 MyBatis 报的错长什么样，学会读报错
4. 对比之前 JDBC 的代码，体会 MyBatis 省掉了哪些重复工作
5. 是否跟敲：**全篇重点，是否跟着敲代码看自己的掌握程度**

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天开始接触「贪心」入门，挑 **1 道简单题**
- 策略：先看题解学思路，不要求独立做出来；分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] MyBatis 连库成功，跑通第一个查询
- [ ] 写过用户表的增删改查四个 Mapper 方法
- [ ] 理解 `#{}` 与字符串拼接的区别（SQL 注入）
- [ ] 今日刷题 1 道（贪心入门简单题，先看题解）
- [ ] 能讲清 MyBatis 相比原生 JDBC 省掉了哪些事
