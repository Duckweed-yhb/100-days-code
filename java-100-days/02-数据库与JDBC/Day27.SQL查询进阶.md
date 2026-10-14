# Day 27 · SQL 查询进阶

> 阶段：02-数据库与JDBC ｜ 重要程度：5.0 ｜ 源视频：https://www.bilibili.com/video/BV1Kr4y1i7ru/

## 今日目标

- 掌握 DQL 查询进阶：多表连接、子查询、聚合函数的基本写法
- 能写出带 JOIN 与 GROUP BY 的常见业务查询
- 理解「SQL 基础」在面试八股中的分量

## 学习内容

- **源路线**：MySQL 建议 4-5 天学完，重要程度 **5.0**，八股四大件之一，面试八股重灾区
  - 视频：https://www.bilibili.com/video/BV1Kr4y1i7ru/
  - 源文案重点：SQL 基础、事务、索引、SQL 优化、锁、InnoDB 存储引擎、日志、主从复制、分库分表、读写分离
- 今日进度：在 Day26 增删改的基础上，进入查询（DQL）部分——条件查询、排序、聚合、多表连接、子查询
- 源文案提示：**没有代码，不用敲代码**（本阶段以理解与 SQL 练习为主，不写 Java 代码）

## 核心重点

- 条件查询：`WHERE`、`LIKE`、`BETWEEN`、`IN`、`IS NULL`
- 排序与分页：`ORDER BY`、`LIMIT`
- 聚合函数：`COUNT` / `SUM` / `AVG` / `MAX` / `MIN`
- 分组查询：`GROUP BY` 与 `HAVING`
- 多表连接：内连接 `INNER JOIN`、左/右外连接 `LEFT/RIGHT JOIN`
- 子查询：`WHERE` 中嵌套 SELECT、`IN` 子查询
- 别名与多表查询的执行顺序直觉

## 练习/任务

1. 基于 Day26 建的业务表，写出带 WHERE 条件与 ORDER BY 的查询
2. 练一道「分组统计」：按某字段 GROUP BY 后用聚合函数统计
3. 自己造两张有关联的表（如用户表与订单表），写一条 INNER JOIN 查询
4. 本阶段不写 Java 代码，把重点放在 SQL 本身

## 今日刷题

- 打开力扣「高频 SQL50 题（基础版）」题单（https://leetcode.cn/studyplan/）
- 今天主攻 **JOIN / 聚合类**，做 **1-2 道**
- 同时从 hot100 挑 **1 道简单题**，与 SQL 穿插保持手感
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] 完成多表 JOIN 与 GROUP BY 查询练习
- [ ] 理解内连接与外连接的区别
- [ ] 完成 1-2 道高频 SQL50（JOIN/聚合类）
- [ ] 完成 1 道 hot100 简单题
- [ ] 记录今日 SQL 报错与解决过程
