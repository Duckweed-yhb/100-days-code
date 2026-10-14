# Day 40 · SSM 之 Spring 核心

> 阶段：03-Web与框架入门 ｜ 重要程度：4.0 ｜ 源视频：https://www.bilibili.com/video/BV1Fi4y1S7ix/

## 今日目标

- 理解 Spring 是干什么的，为什么企业都用它
- 搞懂 IoC / DI 思想：对象不再自己 new，交给容器管理
- 入门 AOP：横切逻辑（如日志、事务）如何不侵入业务代码

## 学习内容

- **源路线**：SSM（Spring、SpringMVC、Mybatis）时间看个人情况，重要程度 **4.0**，三大框架，但面试不咋问，现在也不会单独用，都用 SpringBoot 整合这些东西了，但属于底层基础，SpringBoot 只是整合，其底层还是这三大框架
  - 黑马路线：https://www.bilibili.com/video/BV1Fi4y1S7ix/
  - 源文案重点：**全篇重点，是否跟着敲代码看自己的掌握程度**；这里面的 m 其实是 maven，因为黑马的 JavaWeb 中已经简单介绍了 Mybatis 了
- 今日进度：Spring 简介、IoC 容器与 Bean、DI 依赖注入、基于 XML / 注解配置、AOP 入门
- 源文案提示：**全篇重点**，是否跟着敲代码看自己的掌握程度

## 核心重点

- Spring 是什么：一个轻量级的 Java 开发框架，核心是 IoC 与 AOP
- IoC（控制反转）：对象的创建与交给容器，而不是自己 new
- DI（依赖注入）：容器把对象需要的依赖自动塞进来
- Bean 的概念：被 Spring 容器管理的对象
- 基于 XML 配置 Bean，与基于注解（`@Component` / `@Service` / `@Repository` / `@Controller`）配置 Bean
- `@Autowired` 按类型自动注入
- 组件扫描：`<context:component-scan>` / `@ComponentScan`
- AOP（面向切面编程）：把日志、事务等横切逻辑从业务代码里抽出来
- 切面、切点、通知的基本概念（初步认识即可）

## 练习/任务

1. 新建一个 Maven 项目，引入 Spring 核心依赖，跑通第一个 IoC 小例子（从容器里 `getBean` 拿到对象）
2. 写两个有依赖关系的类，分别用 XML 和注解两种方式完成 DI 注入
3. 跟着视频写一个最简单的 AOP 日志切面，看方法被调用时切面如何生效
4. 是否跟敲：**全篇重点，是否跟着敲代码看自己的掌握程度**，掌握得好可以只看，掌握得弱就多敲

## 今日刷题

- 打开力扣「hot100」题单（https://leetcode.cn/studyplan/）
- 今天开始接触「回溯 / 递归」入门，挑 **1 道简单题**
- 策略：先看题解学思路，不要求独立做出来；分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目要反复刷

## 打卡清单

- [ ] 跑通第一个 Spring IoC 例子，从容器拿到 Bean
- [ ] 会用 `@Component` / `@Autowired` 完成注解方式的依赖注入
- [ ] 理解 AOP 在解决什么问题（不要求手写复杂切面）
- [ ] 今日刷题 1 道（回溯 / 递归入门简单题，先看题解）
- [ ] 用自己的话讲清 IoC / DI 是什么意思
