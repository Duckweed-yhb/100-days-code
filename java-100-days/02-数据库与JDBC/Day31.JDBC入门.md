# Day 31 · JDBC 入门

> 阶段：02-数据库与JDBC ｜ 重要程度：2.0 ｜ 源视频：https://www.bilibili.com/video/BV1s3411K7jH/

## 今日目标

- 理解 JDBC 的作用：用 Java 操作 MySQL
- 跟着视频敲一点点 JDBC 代码，跑通连接、查询、更新的基本流程
- 认识 JDBC 已被 MyBatis / Spring 等框架封装，了解底层原理即可

## 学习内容

- **源路线**：JDBC 建议 0.5 天学完，重要程度 **2.0**，主要是把 MySQL 和 Java 结合起来，如何用 Java 操作 MySQL
  - 视频：https://www.bilibili.com/video/BV1s3411K7jH/
  - 源文案提示：**但是已被框架封装，了解即可**
- 今日进度：JDBC 核心 API 与基本步骤——注册驱动、获取连接、创建 Statement、执行 SQL、处理结果集、释放资源
- 源文案提示：**稍微跟着敲一点点代码即可，不怎么重要，但其实是底层原理，还是需要了解的**

## 核心重点

- JDBC（Java Database Connectivity）：Java 访问关系型数据库的标准 API
- 核心接口：`DriverManager`、`Connection`、`Statement` / `PreparedStatement`、`ResultSet`
- 基本流程：加载驱动 → 获取连接 → 执行 SQL → 遍历结果集 → 关闭资源
- `PreparedStatement` 与 SQL 注入的基本概念
- JDBC 是 MyBatis、Spring JDBC 等框架的底层原理
- 实际开发中很少裸写 JDBC，由框架封装

## 练习/任务

1. 导入 MySQL 驱动包，写一段 JDBC 代码连上本机 MySQL
2. 用 `Statement` / `PreparedStatement` 执行一条 SELECT，遍历 `ResultSet` 打印结果
3. 执行一条 INSERT 或 UPDATE，观察数据库变化
4. 按规范在 finally 或 try-with-resources 中关闭连接资源

## 今日刷题

- 打开力扣「高频 SQL50 题（基础版）」题单（https://leetcode.cn/studyplan/）
- 今天做 **1 道**即可，保持手感
- 同时从 hot100 挑 **1 道简单题**
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] 跑通第一段 JDBC 连接代码
- [ ] 用 PreparedStatement 完成一次查询与一次更新
- [ ] 能说清 JDBC 与 MyBatis 等框架的关系
- [ ] 完成 1 道高频 SQL50
- [ ] 完成 1 道 hot100 简单题
