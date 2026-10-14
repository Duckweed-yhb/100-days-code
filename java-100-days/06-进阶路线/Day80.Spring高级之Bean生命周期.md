# Day 80 · Spring 高级之 Bean 生命周期

> 阶段：06-进阶路线 ｜ 重要程度：源文案未标注 ｜ 源视频：https://www.bilibili.com/video/BV1P44y1N7QG/

## 今日目标

- 进入「进阶路线」阶段，明确本阶段是把 Java 技术栈往深里走的开始
- 跟着 Spring 高级课程，建立对 IoC 容器内部运作的整体认识
- 把 Bean 生命周期的关键环节串成一条线，能说清一个 Bean 从创建到销毁经历了什么

## 学习内容

- **源路线**：进阶路线之 Spring 高级
  - 视频：https://www.bilibili.com/video/BV1P44y1N7QG/
  - 本阶段共 2 天（Day80、Day81），今天先过 Bean 生命周期
- 今日进度：Spring 容器概览、Bean 的定义与注册、实例化与属性填充、Aware 接口与 BeanPostProcessor、初始化与销毁回调
- 源文案提示：至此 Java 基础已经学完，进阶部分重在理解原理，不必每行代码都敲

## 核心重点

- IoC 容器的职责：Bean 的注册、实例化、依赖注入与生命周期管理
- Bean 生命周期主线：实例化 → 属性填充 → 初始化 → 使用 → 销毁
-  BeanNameAware / BeanFactoryAware / ApplicationContextAware 等 Aware 接口的回调时机
- BeanPostProcessor 的 postProcessBeforeInitialization 与 postProcessAfterInitialization
- `@PostConstruct`、InitializingBean、`init-method` 三种初始化方式的先后
- 循环依赖的产生场景与三级缓存的大致作用（只作名词了解）
- Bean 的作用域（singleton / prototype 等）对生命周期的影响
- 销毁回调：`@PreDestroy`、DisposableBean、`destroy-method`

## 练习/任务

1. 跟着视频写一个最简单的 Bean，实现 InitializingBean 与 DisposableBean，在每个回调方法里打印日志
2. 自定义一个 BeanPostProcessor，在前后处理方法中打印 Bean 名称，观察它对容器里所有 Bean 都生效
3. 画一张 Bean 生命周期流程图（标注 Aware、构造、属性填充、初始化、销毁的顺序），放进笔记

## 今日刷题

- hot100 进入第 2 遍收尾 / 第 3 遍起步阶段，今天 2 道
- 期间穿插 LeetCode 75 题单做分类补充：今天可挑「哈希」或「双指针」分类里还不熟的题
- 策略：分类刷，简单 → 中等 → 困难；一道题 5 分钟没思路，直接看题解，从题解中举一反三；题目量不需要很多，但要反复刷

## 打卡清单

- [ ] 看完 Spring 高级课程中 Bean 生命周期对应章节
- [ ] 跑通带初始化/销毁回调的示例 Bean
- [ ] 能在白纸上画出 Bean 生命周期主线
- [ ] 今日刷题 2 道（hot100 二刷/三刷，或 LeetCode 75 分类补充）
- [ ] 记录今天看不懂的点，留待后面回头再看
